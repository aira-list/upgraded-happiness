# Project Documentation

This repository powers our team’s documentation website using [MkDocs](https://www.mkdocs.org/), deployed automatically to GitHub Pages.

## Branch Workflow

We use a structured branching strategy for safe collaboration and automated deployment:


| Branch | Push Allowed | Pull Request Required | Deployment | Role |
| :-- | :-- | :-- | :-- | :-- |
| main | No | Yes | Yes (auto to GitHub Pages) | Production/release branch |
| develop | Yes | Optional | No | Active development/staging |

- **Contributors work in `develop`:**
    - Pushes and pull requests allowed.
    - Images, assets, and new docs are added here.
- **Production deployment only from `main`:**
    - Direct pushes are blocked.
    - All merges to `main` must use pull requests.
    - Automated deployment runs on each merge to `main`.
 

## How to Contribute to Our Website Using GitHub (No Coding Required)

Thank you for helping us improve our documentation! You can suggest changes, upload images, or fix typos directly through the GitHub website. No coding skills required—just follow these steps:

### Step 1: Sign in and Access the Repository

1. Go to our project’s GitHub repository:  
   _[Here](https://github.com/aira-list/upgraded-happiness)_
2. Log in with your GitHub account.  
   If you don’t have one, sign up [here](https://github.com/join).

***

### Step 2: Locate the File or Folder You Want to Edit on the `develop` Branch

1. Select the `develop` branch when you're on the [project's home page](https://github.com/aira-list/upgraded-happiness)
3. Use the file browser to navigate to the documentation you want to update.  
   Look inside the `docs/` folder for website pages and images.
4. To **edit text** (e.g., fixing a typo or adding a section):
   - Click the file name (for example, `docs/index.md`).
   - Click the pencil icon (✏️) in the top right to edit.
5. To **upload images or add new files**:
   - Click the `docs/` folder.
   - Click the "Add file" button and choose "Upload files" or "Create new file."

***

### Step 3: Make Your Edits or Uploads

- For text files: Use the built-in editor. Write or paste your changes.
- For images: Drag and drop your file, or click to select it on your computer.

***

### Step 4: Propose Your Changes via a Pull Request (PR)

1. **Commit your changes**:
   - At the bottom of the edit/upload page, fill out a summary of your changes (e.g., "Added an introduction section" or "Uploaded team photo").
   - Select “Create a new branch for this commit and start a pull request.”
   - Click **Propose changes**.

2. **Create a Pull Request**:
   - On the next page, check your edits and click **Create pull request**.
   - Write a short description about what you did (e.g., "Fixed spelling errors on Home page" or "Added illustration to Getting Started guide").
   - Click **Create pull request** again.

***

### Step 5: Review & Approval

- Our team will review your pull request and may leave comments for clarification or suggestions.
- If everything looks good, we’ll approve and merge your changes so they appear on the website!
- You’ll be notified about progress or feedback via email or GitHub notifications.

***

### Tips & Good Practices

- **Be specific**: When describing your contribution, tell us exactly what you changed or added.
- **Preview changes**: Double-check your edits in the web editor before proposing.
- **Ask for help**: If you’re unsure, just add your question in the PR description or open an Issue.

***

### Frequently Asked Questions

**Q: Will I break the website with my changes?**  
A: No! All contributions are reviewed before going live, so we’ll help fix any issues.

**Q: Can I upload more than one file at a time?**  
A: Yes, you can select and upload multiple images when using the “Upload files” button.

**Q: What if I need to edit after I submit?**  
A: You can push more changes to your PR branch or ask for help in the comments.

***

### Thank You!

All contributions, big or small, help make our documentation better. Don’t hesitate to suggest ideas, fix mistakes, or add content—your help is appreciated!

***

**For any questions or support, please contact the maintainers or open an Issue in the repository.**


## Contribution Workflow (For Technical Users)

1. **Fork or clone the repository.**
2. **Switch to the `develop` branch:**

```bash
git checkout develop
```

3. **Add new docs, images, or assets to the `docs/` folder.**
4. **Push changes to `develop` directly, or open a pull request targeting `develop`.**
5. **Once ready for release, open a pull request from `develop` to `main`.**
    - All PRs into `main` are reviewed and must pass CI checks (build, lint, etc.).
    - Only after PR approval will your changes be merged and deployed.

### Automated Deployment

- **Deployment is triggered automatically on push or merge to `main`.**
- Our [GitHub Actions](.github/workflows/deploy.yml) workflow builds the MkDocs site and publishes the latest version to the `gh-pages` branch for GitHub Pages hosting.


### Guidelines for Contributors

- Always add images and files using **relative paths** inside `docs/` for portability.
- Do not push directly to `main`; use PRs for all updates.
- Keep feature additions, fixes, or uploads scoped to individual pull requests when possible.


### Quick Commands

```bash
# Clone repository
git clone https://github.com/aira-list/upgraded-happiness.git
cd your-repo

# Switch to develop
git checkout develop

# Make changes (edit docs, add images)

# Stage and commit
git add docs/...
git commit -m "Add new docs/assets"

# Push changes
git push origin develop

# Open a PR targeting develop or main as needed
```


## Maintainers#

- Merges to `main` must be made via pull request after review.
- Always verify CI checks and deployment status post-merge.


## Support

For help, submit an issue or contact project maintainers.
