# Git Workflow for Collaborative Projects

This document outlines the Git workflow that all collaborators in this project should follow to ensure a clean, manageable, and efficient development process. By following these guidelines, we can minimize merge conflicts, maintain a clear project history, avoid the common problem of excluding commits from the main/prod branch when merging a feature and facilitate smooth collaboration.

## Core Principles

* **Isolate Features:** Each new feature or bug fix should be developed in its own dedicated branch.
* **Keep `main` or `prod` Production-Ready:** The `main` or `prod` branch should always reflect the stable, production-ready state of the codebase.
* **Integrate Frequently:** Small, well-tested changes are easier to manage and integrate.
* **Clear Commit Messages:** Write informative and concise commit messages that explain the purpose of each change (more on this below).
* **Code Review is Essential:** All changes should be reviewed by at least one other team member before being merged into `main` or `prod`.

## Branching Strategy

We utilize the following long-lived branches:

* **`main` or `prod`:** The primary branch representing the stable, production-ready codebase. **Do not commit directly to `main` or `prod`.**
* **`staging`:** An integration branch for features that are nearing completion and are being tested together. Experimental or incomplete features may also reside here temporarily. Assume this branch to be unstable and in no way suitable to be merged directly into `main` or `prod`.

And the following short-lived branches:

* **`feature/*` (e.g., `feature/user-authentication`, `feature/payment-integration`):** Branches created from `staging` for developing new features.
* **`bugfix/*` (e.g., `bugfix/login-issue`, `bugfix/data-corruption`):** Branches created from `main` or `prod` to address specific bugs in the production code.
* **`hotfix/*` (e.g., `hotfix/security-vulnerability`):** Branches created directly from `main` or `prod` for critical, immediate fixes that need to bypass the regular release cycle.

* If multipe collaborators are working on the same feature, consider having 2 levels of branching, e.g. (create a feature branch, `feature/user-payment`, then create further branches from this branch such as, `feature/user-payment-habib`, `feature/user-payment-anwar`, etc.). Then reabse onto `feature/user-payment`, and merge this onto the respective rebased branch (this will be a fast forward). 

## Workflow Steps

Follow these steps for contributing changes to the repository:

**1. Start a New Feature:**

* Ensure your local `staging` branch is up-to-date:
    ```bash
    git checkout staging
    git pull origin staging
    ```
* Create a new feature branch based on `staging`:
    ```bash
    git checkout -b feature/<your-feature-name>
    ```
    * Replace `<your-feature-name>` with a descriptive name for your feature.

**2. Develop Your Feature:**

* Make your code changes and commit them regularly with clear and concise commit messages:
    ```bash
    git add .
    git commit -m "feat: Add initial implementation of user authentication"
    git commit -m "fix: Address minor UI issues on the login page"
    # ... more commits as needed
    ```

**2. Squash your commits on the feature branch (Optional but recommended):**

* 

**4. Integrate Changes (Rebasing onto Integration Branch):**

* Once your feature is complete and tested locally, prepare it for integration. First, create an integration branch from `main`:
    ```bash
    git checkout main
    git pull origin main
    git checkout -b main-feature/<your-feature-name>
    ```
* Rebase your feature branch onto this new integration branch:
    ```bash
    git rebase main-feature/<your-feature-name> feature/<your-feature-name>
    ```
    * The above command is equivalent to executing `git checkout feature/<your-feature-name>` then `git rebase main-feature/<your-feature-name>`
    * If you encounter conflicts during the rebase, resolve them carefully using `git add <conflicted_file>` and `git rebase --continue`. If necessary, you can abort the rebase with `git rebase --abort`.

**5. Merge Feature Branch into Integration Branch:**

* Checkout your integration branch:
    ```bash
    git checkout main-feature/<your-feature-name>
    ```
* Merge your rebased feature branch into the integration branch:
    ```bash
    git merge feature/<your-feature-name>
    ```
    * This should typically be a fast-forward merge if the rebase was successful.

**6. Push Your Integration Branch:**

* Push your integration branch to the remote repository:
    ```bash
    git push origin main-feature/<your-feature-name>
    ```

**7. Create a Pull Request (PR):**

* Go to the repository on GitHub.
* Create a new Pull Request.
* Set the **base branch** to `main`.
* Set the **compare branch** to `main-feature/<your-feature-name>`.
* Provide a clear title and a detailed description of the changes in your PR. Include any relevant context, testing information, and screenshots if applicable.
* Request a review from at least one other team member.

**8. Code Review and Discussion:**

* Address any feedback or comments provided during the code review.
* Make necessary changes and push them to your `main-feature/<your-feature-name>` branch. The PR will automatically update.
* Maintain a respectful and constructive discussion with your reviewers.

**9. Merge Pull Request:**

* Once the code review is complete and all issues are resolved, a designated team member will merge the PR into the `main` branch.

**10. Clean Up:**

* After your PR has been merged, you can safely delete your local feature branch and the remote integration branch:
    ```bash
    git checkout main
    git pull origin main
    git branch -d feature/<your-feature-name>
    git push origin --delete main-feature/<your-feature-name>
    ```

## Bug Fix Workflow

1.  **Create a Bugfix Branch:**
    ```bash
    git checkout main
    git pull origin main
    git checkout -b bugfix/<issue-description>
    ```

2.  **Fix the Bug and Commit:**
    ```bash
    # Make necessary changes
    git add .
    git commit -m "fix: Resolve issue with [brief description of bug]"
    ```

3.  **Create a Pull Request:** Create a PR from `bugfix/<issue-description>` to `main`.

4.  **Review and Merge:** Follow the same review and merge process as with feature branches.

5.  **Clean Up:** Delete your local bugfix branch after merging.

## Hotfix Workflow (Use with Caution)

1.  **Create a Hotfix Branch:**
    ```bash
    git checkout main
    git pull origin main
    git checkout -b hotfix/<critical-issue>
    ```

2.  **Fix the Hotfix and Commit:**
    ```bash
    # Make necessary changes
    git add .
    git commit -m "hotfix: Immediately address critical security vulnerability"
    ```

3.  **Create a Pull Request:** Create a PR from `hotfix/<critical-issue>` to `main`. **Ensure this PR is prioritized for review and merging.**

4.  **Merge and Deploy:** Once reviewed and approved, merge the hotfix PR to `main` and deploy immediately.

5.  **Merge to `staging`:** After the hotfix is on `main`, merge `main` into `staging` to ensure the fix is also included in the ongoing development:
    ```bash
    git checkout staging
    git pull origin staging
    git merge main
    git push origin staging
    ```

6.  **Clean Up:** Delete your local hotfix branch after merging.

## Commit Message Guidelines

* Use the present tense ("Add feature" not "Added feature").
* Use the imperative mood ("Fix bug" not "Fixes bug").
* The first line should be concise (max 50 characters) and summarize the change. **It should also include the relevant Jira ticket number at the beginning.**
* Separate the subject from the body with a blank line.
* The body should provide more detailed context and explanation of the changes.
* Consider using prefixes to indicate the type of commit (followed by the Jira ticket number):
    * `feat`: A new feature (e.g., `feat: [PROJECT-123] Add user profile page`)
    * `fix`: A bug fix (e.g., `fix: [PROJECT-456] Resolve login issue on mobile`)
    * `docs`: Documentation changes (e.g., `docs: [PROJECT-789] Update API documentation`)
    * `style`: Changes that do not affect the meaning of the code (e.g., `style: [PROJECT-101] Apply consistent code formatting`)
    * `refactor`: A code change that neither adds a feature nor fixes a bug (e.g., `refactor: [PROJECT-112] Extract common utility function`)
    * `test`: Adding missing or correcting existing tests (e.g., `test: [PROJECT-131] Add unit tests for payment service`)
    * `chore`: Changes to the build process or auxiliary tools (e.g., `chore: [PROJECT-141] Update Gradle dependencies`)

* **Include the Jira ticket number enclosed in square brackets at the beginning of the first line of your commit message.** For example: `feat: [PROJECT-123] Implement user authentication`

**Example Commit Messages:**

1. [WN-123] Fix: Prevent null pointer exception in order processing

This commit addresses an issue where a null pointer exception could occur
during order processing under specific circumstances. Added a null check
to prevent this.

2. [WN-124] Feat: Implement user profile page with basic information

Introduced a new user profile page displaying the user's name, email,
and registration date. Added necessary API endpoints and UI components.

## Important Reminders

* **Pull Regularly:** Always pull the latest changes from the remote repository before starting new work.
* **Communicate:** If you are unsure about any step or encounter issues, don't hesitate to ask for help from your team members.
* **Keep Branches Focused:** Each branch should address a single feature or bug fix.
* **Avoid Committing Large Changes:** Break down large features into smaller, logical commits.
* **Be Mindful of History:** Understand the implications of rebasing and force-pushing. Avoid force-pushing to shared branches.

By following this Git workflow, we can maintain a healthy and collaborative development environment. Let's all strive to adhere to these guidelines for the benefit of our projects and our team.
