# Open Authoring



## 

**This is a** [**beta feature**](https://www.netlifycms.org/docs/beta-features#open-authoring)**.**

When using the [GitHub backend](https://www.netlifycms.org/docs/github-backend), you can use Netlify CMS to accept contributions from GitHub users without giving them access to your repository. When they make changes in the CMS, the CMS forks your repository for them behind the scenes, and all the changes are made to the fork. When the contributor is ready to submit their changes, they can set their draft as ready for review in the CMS. This triggers a pull request to your repository, which you can merge using the GitHub UI.

At the same time, any contributors who _do_ have write access to the repository can continue to use Netlify CMS normally.

### Requirements <a id="requirements"></a>

* You must use [the GitHub backend](https://www.netlifycms.org/docs/github-backend).

  **Note that the** [**Git Gateway backend**](https://www.netlifycms.org/docs/git-gateway-backend/#git-gateway-with-netlify-identity) **does** _**not**_ **support Open Authoring, even when the underlying repo is on GitHub.**

* For private GitHub repos the user must have `read` access on the repo, and you must explicitly set the auth\_scope to `repo`, for example:

```text
backend:
  name: github
  repo: owner-name/private-repo-name # path to private repo
  auth_scope: repo # this is needed to fork the private repo
  open_authoring: true
```

### Enabling Open Authoring <a id="enabling-open-authoring"></a>

1. [Enable the editorial workflow](https://www.netlifycms.org/docs/configuration-options/#publish-mode) by setting `publish_mode` to `editorial_workflow` in your `config.yml`.
2. Set `open_authoring` to `true` in the `backend` section of your `config.yml`, as follows:

   ```text
   backend:
     name: github
     repo: owner-name/repo-name # Path to your GitHub repository
     open_authoring: true
   ```

### Usage <a id="usage"></a>

When a user logs into Netlify CMS who doesn't have write access to your repo, the CMS asks for permission to create a fork of your repo \(or uses their existing fork, if they already have one\). They are then presented with the normal CMS interface. The published content shown is from the original repo, so it stays up-to-date as changes are made.

On the editorial workflow screen, the normal three columns are replaced by two columns instead — "Draft" and "Ready to Review".

When they make changes to content in the CMS, the changes are made to a branch on their fork. In the editorial workflow screen, they see only their own pending changes. Once they're ready to submit their changes, they can move the card into the "Ready To Review" column to create a pull request. When the pull request is merged \(by a repository maintainer via the GitHub UI\), Netlify CMS deletes the branch and removes the card from the user's editorial workflow screen. Open Authoring users cannot publish entries through the CMS.

Users who _do_ have write access to the original repository continue to use the CMS normally. Unpublished changes made by users via Open Authoring are not visible on the editorial workflow screen, and their unpublished changes must be merged through the GitHub UI.

### Alternative for external contributors with Git Gateway <a id="alternative-for-external-contributors-with-git-gateway"></a>

[As noted above](https://www.netlifycms.org/docs/open-authoring/#requirements), Open Authoring does not work with the Git Gateway backend. However, you can use Git Gateway on a site with Netlify Identity that has [open registration](https://www.netlify.com/docs/identity/#adding-identity-users). This lets users create accounts on your site and log into the CMS. There are a few differences, including the following:

* Users don't need to know about GitHub or create a GitHub account. Instead, they use Netlify Identity accounts that are created on your site and managed by you.
* The CMS applies users' changes directly to your repo, not to a fork. \(If you use the editorial workflow, you can use features like [GitHub's protected branches](https://help.github.com/en/articles/about-protected-branches) or [Netlify's locked deploys](https://www.netlify.com/docs/locked-deploys/) to prevent users from publishing directly to your site from the CMS.\)
* There is no distinction between users with write access to the repo and users without — all editorial workflow entries are visible from within the CMS and can be published with the CMS. \(Unpublished Open Authoring entries, on the other hand, are visible only to the author in the CMS UI or publicly as GitHub PRs.\)

### Linking to specific entries in the CMS <a id="linking-to-specific-entries-in-the-cms"></a>

Open authoring often includes some sort of "Edit this page" link on the live site. Netlify CMS supports this via the **edit** path:

```text
/#/edit/{collectionName}/{entryName}
```

For the entry named "general" in the "settings" file collection

```text
https://www.example.com/path-to-cms/#/edit/settings/general
```

For blog post "test.md" in the "posts" folder collection

```text
https://www.example.com/path-to-cms/#/edit/posts/test
```

* **`collectionName`**: the name of the collection as entered in the CMS config.
* **`entryName`** _\(for_ [_file collections_](https://www.netlifycms.org/docs/collection-types/#file-collections): the `name` of the entry from the CMS config.
* **`entryName`** _\(for_ [_folder collections_](https://www.netlifycms.org/docs/collection-types/#folder-collections): the filename, sans extension \(the slug\).

