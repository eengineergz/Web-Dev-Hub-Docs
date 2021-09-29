---
description: initial page
---

# Home

## Beta Features!

We run new functionality in an open beta format from time to time. That means that this functionality is totally available for use, and we _think_ it might be ready for primetime, but it could break or change without notice.

**Use these features at your own risk.**

### Working with a Local Git Repository <a id="working-with-a-local-git-repository"></a>

_**added in**_ [_**netlify-cms@2.10.17**_](mailto:netlify-cms@2.10.17) _**/**_ [_**netlify-cms-app@2.11.14**_](mailto:netlify-cms-app@2.11.14)

You can connect Netlify CMS to a local Git repository, instead of working with a live repo.

1. Navigate to a local Git repository configured with the CMS.
2. Add the top-level property `local_backend` configuration to your `config.yml`:

```text
backend:
  name: git-gateway

# when using the default proxy server port
local_backend: true
```

1. Run `npx netlify-cms-proxy-server` from the root directory of the above repository.
   * If the default port \(8081\) is in use, the proxy server won't start and you will see an error message. In this case, follow [these steps](https://www.netlifycms.org/docs/beta-features/#configure-the-netlify-cms-proxy-server-port-number) before proceeding.
2. Start your local development server \(e.g. run `gatsby develop`\).
3. Open [http://localhost:8000/admin](http://localhost:8000/admin) to verify that your can administer your content locally.

**Note:** `netlify-cms-proxy-server` runs an unauthenticated express server. As any client can send requests to the server, it should only be used for local development.

#### Configure the Netlify CMS proxy server port number <a id="configure-the-netlify-cms-proxy-server-port-number"></a>

1. Create a `.env` file in the project's root folder and define the PORT you'd like the proxy server to use

```text
PORT=8082
```

1. Update the `local_backend` object in `config.yml` and specify a `url` property to use your custom port number

```text
backend:
  name: git-gateway

local_backend:
  # when using a custom proxy server port
  url: http://localhost:8082/api/v1
  # when accessing the local site from a host other than 'localhost' or '127.0.0.1'
  allowed_hosts: ['192.168.0.1']
```

### GitLab and BitBucket Editorial Workflow Support <a id="gitlab-and-bitbucket-editorial-workflow-support"></a>

_**added in**_ [_**netlify-cms@2.10.6**_](mailto:netlify-cms@2.10.6) _**/**_ [_**netlify-cms-app@2.11.3**_](mailto:netlify-cms-app@2.11.3)

You can enable the Editorial Workflow with the following line in your Netlify CMS `config.yml` file:

```text
publish_mode: editorial_workflow
```

In order to track unpublished entries statuses the GitLab implementation uses merge requests labels and the BitBucket implementation uses pull requests comments.

### i18n Support <a id="i18n-support"></a>

The CMS can provide a side by side interface for authoring content in multiple languages. Configuring the CMS for i18n support requires top level configuration, collection level configuration and field level configuration.

#### Top level configuration <a id="top-level-configuration"></a>

```text
i18n:
  # Required and can be one of multiple_folders, multiple_files or single_file
  # multiple_folders - persists files in `<folder>/<locale>/<slug>.<extension>`
  # multiple_files - persists files in `<folder>/<slug>.<locale>.<extension>`
  # single_file - persists a single file in `<folder>/<slug>.<extension>`
  structure: multiple_folders

  # Required - a list of locales to show in the editor UI
  locales: [en, de, fr]

  # Optional, defaults to the first item in locales.
  # The locale to be used for fields validation and as a baseline for the entry.
  default_locale: en
```

#### Collection level configuration <a id="collection-level-configuration"></a>

```text
collections:
  - name: i18n_content
    # same as the top level, but all fields are optional and defaults to the top level
    # can also be a boolean to accept the top level defaults
    i18n: true
```

When using a file collection, you must also enable i18n for each individual file:

```text
collections:
  - name: pages
    label: Pages
    # Configure i18n for this collection.
    i18n:
      structure: single_file
      locales: [en, de, fr]
    files:
      - name: about
        label: About Page
        file: site/content/about.yml
        # Enable i18n for this file.
        i18n: true
        fields:
          - { label: Title, name: title, widget: string, i18n: true }
```

#### Field level configuration <a id="field-level-configuration"></a>

```text
fields:
  - label: Title
    name: title
    widget: string
    # same as 'i18n: translate'. Allows translation of the title field
    i18n: true
  - label: Date
    name: date
    widget: datetime
    # The date field will be duplicated from the default locale.
    i18n: duplicate
  - label: Body
    name: body
    # The markdown field will be omitted from the translation.
    widget: markdown
```

Example configuration:

```text
i18n:
  structure: multiple_folders
  locales: [en, de, fr]

collections:
  - name: posts
    label: Posts
    folder: content/posts
    create: true
    i18n: true
    fields:
      - label: Title
        name: title
        widget: string
        i18n: true
      - label: Date
        name: date
        widget: datetime
        i18n: duplicate
      - label: Body
        name: body
        widget: markdown
```

#### Limitations <a id="limitations"></a>

1. File collections support only `structure: single_file`.
2. List widgets only support `i18n: true`. `i18n` configuration on sub fields is ignored.
3. Object widgets only support `i18n: true` and `i18n` configuration should be done per field:

```text
- label: 'Object'
  name: 'object'
  widget: 'object'
  i18n: true
  fields:
    - { label: 'String', name: 'string', widget: 'string', i18n: true }
    - { label: 'Date', name: 'date', widget: 'datetime', i18n: duplicate }
    - { label: 'Boolean', name: 'boolean', widget: 'boolean', i18n: duplicate }
    - {
        label: 'Object',
        name: 'object',
        widget: 'object',
        i18n: true,
        field: { label: 'String', name: 'string', widget: 'string', i18n: duplicate },
      }
```

### GitHub GraphQL API <a id="github-graphql-api"></a>

Experimental support for GitHub's [GraphQL API](https://developer.github.com/v4/) is now available for the GitHub backend.

**Note: not currently compatible with Git Gateway.**

For many queries, GraphQL allows data to be retrieved using less individual API requests compared to a REST API. GitHub's GraphQL API still does not support all mutations necessary to completely replace their REST API, so this feature only calls the new GraphQL API where possible.

You can use the GraphQL API for the GitHub backend by setting `backend.use_graphql` to `true` in your CMS config:

```text
backend:
  name: github
  repo: owner/repo # replace this with your repo info
  use_graphql: true
```

Learn more about the benefits of GraphQL in the [GraphQL docs](https://graphql.org/).

### Open Authoring <a id="open-authoring"></a>

When using the [GitHub backend](https://www.netlifycms.org/docs/github-backend), you can use Netlify CMS to accept contributions from GitHub users without giving them access to your repository. When they make changes in the CMS, the CMS forks your repository for them behind the scenes, and all the changes are made to the fork. When the contributor is ready to submit their changes, they can set their draft as ready for review in the CMS. This triggers a pull request to your repository, which you can merge using the GitHub UI.

At the same time, any contributors who _do_ have write access to the repository can continue to use Netlify CMS normally.

More details and setup instructions can be found on [the Open Authoring docs page](https://www.netlifycms.org/docs/open-authoring).

### Folder Collections Path <a id="folder-collections-path"></a>

By default the CMS stores folder collection content under the folder specified in the collection setting.

For example configuring `folder: posts` for a collection will save the content under `posts/post-title.md`.

You can now specify an additional `path` template \(similar to the `slug` template\) to control the content destination.

This allows saving content in subfolders, e.g. configuring `path: '{{year}}/{{slug}}'` will save the content under `posts/2019/post-title.md`.

### Folder Collections Media and Public Folder <a id="folder-collections-media-and-public-folder"></a>

By default the CMS stores media files for all collections under a global `media_folder` directory as specified in the configuration.

When using the global `media_folder` directory any entry field that points to a media file will use the absolute path to the published file as designated by the `public_folder` configuration.

For example configuring:

```text
media_folder: static/media
public_folder: /media
```

And saving an entry with an image named `image.png` will result in the image being saved under `static/media/image.png` and relevant entry fields populated with the value of `/media/image.png`.

Some static site generators \(e.g. Gatsby\) work best when using relative image paths.

This can now be achieved using a per collection `media_folder` configuration which specifies a relative media folder for the collection.

For example, the following configuration will result in media files being saved in the same directory as the entry, and the image field being populated with the relative path to the image.

```text
media_folder: static/media
public_folder: /media
collections:
  - name: posts
    label: Posts
    label_singular: 'Post'
    folder: content/posts
    path: '{{slug}}/index'
    media_folder: ''
    public_folder: ''
    fields:
      - label: Title
        name: title
        widget: string
      - label: 'Cover Image'
        name: 'image'
        widget: 'image'
```

More specifically, saving an entry with a title of `example post` with an image named `image.png` will result in a directory structure of:

```text
content
  posts
    example-post
      index.md
      image.png
```

And for the image field being populated with a value of `image.png`.

**Note: When specifying a `path` on a folder collection, `media_folder` defaults to an empty string.**

**Available template tags:**

Supports all of the [`slug` templates](https://www.netlifycms.org/docs/configuration-options#slug) and:

* `{{dirname}}` The path to the file's parent directory, relative to the collection's `folder`.
* `{{filename}}` The file name without the extension part.
* `{{extension}}` The file extension.
* `{{media_folder}}` The global `media_folder`.
* `{{public_folder}}` The global `public_folder`.

### List Widget: Variable Types <a id="list-widget-variable-types"></a>

Before this feature, the [list widget](https://www.netlifycms.org/docs/widgets/#list) allowed a set of fields to be repeated, but every list item had the same set of fields available. With variable types, multiple named sets of fields can be defined, which opens the door to highly flexible content authoring \(even page building\) in Netlify CMS.

**Note: this feature does not yet support default previews and requires** [**registering a preview template**](https://www.netlifycms.org/docs/customization#registerpreviewtemplate) **in order to show up in the preview pane.**

To use variable types in the list widget, update your field configuration as follows:

1. Instead of defining your list fields under `fields` or `field`, define them under `types`. Similar to `fields`, `types` must be an array of field definition objects.
2. Each field definition under `types` must use the `object` widget \(this is the default value for `widget`\).

#### Additional list widget options <a id="additional-list-widget-options"></a>

* `types`: a nested list of object widgets. All widgets must be of type `object`. Every object widget may define different set of fields.
* `typeKey`: the name of the field that will be added to every item in list representing the name of the object widget that item belongs to. Ignored if `types` is not defined. Default is `type`.
* `summary`: allows customization of a collapsed list item object in a similar way to a [collection summary](https://www.netlifycms.org/docs/configuration-options/?#summary)

#### Example Configuration <a id="example-configuration"></a>

The example configuration below imagines a scenario where the editor can add two "types" of content, either a "carousel" or a "spotlight". Each type has a unique name and set of fields.

```text
- label: 'Home Section'
  name: 'sections'
  widget: 'list'
  types:
    - label: 'Carousel'
      name: 'carousel'
      widget: object
      summary: '{{fields.header}}'
      fields:
        - { label: Header, name: header, widget: string, default: 'Image Gallery' }
        - { label: Template, name: template, widget: string, default: 'carousel.html' }
        - label: Images
          name: images
          widget: list
          field: { label: Image, name: image, widget: image }
    - label: 'Spotlight'
      name: 'spotlight'
      widget: object
      fields:
        - { label: Header, name: header, widget: string, default: 'Spotlight' }
        - { label: Template, name: template, widget: string, default: 'spotlight.html' }
        - { label: Text, name: text, widget: text, default: 'Hello World' }
```

#### Example Output <a id="example-output"></a>

The output for the list widget will be an array of objects, and each object will have a `type` key with the name of the type used for the list item. The `type` key name can be customized via the `typeKey` property in the list configuration.

If the above example configuration were used to create a carousel, a spotlight, and another carousel, the output could look like this:

```text
title: Home
sections:
  - type: carousel
    header: Image Gallery
    template: carousel.html
    images:
      - images/image01.png
      - images/image02.png
      - images/image03.png
  - type: spotlight
    header: Spotlight
    template: spotlight.html
    text: Hello World
  - type: carousel
    header: Image Gallery
    template: carousel.html
    images:
      - images/image04.png
      - images/image05.png
      - images/image06.png
```

### Custom Mount Element <a id="custom-mount-element"></a>

Netlify CMS always creates its own DOM element for mounting the application, which means it always takes over the entire page, and is generally inflexible if you're trying to do something creative, like injecting it into a shared context.

You can now provide your own element for Netlify CMS to mount in by setting the target element's ID as `nc-root`. If Netlify CMS finds an element with this ID during initialization, it will mount within that element instead of creating its own.

### Manual Initialization <a id="manual-initialization"></a>

Netlify CMS can now be manually initialized, rather than automatically loading up the moment you import it. The whole point of this at the moment is to inject configuration into Netlify CMS before it loads, bypassing need for an actual Netlify CMS `config.yml`. This is important, for example, when creating tight integrations with static site generators.

Assuming you have the netlify-cms package installed to your project, manual initialization works by setting `window.CMS_MANUAL_INIT = true` **before importing the CMS**:

```text
// This global flag enables manual initialization.
window.CMS_MANUAL_INIT = true
// Usage with import from npm package
import CMS, { init } from 'netlify-cms'
// Usage with script tag
const { CMS, initCMS: init } = window
/**
 * Initialize without passing in config - equivalent to just importing
 * Netlify CMS the old way.
 */
init()
/**
 * Optionally pass in a config object. This object will be merged into
 * `config.yml` if it exists, and any portion that conflicts with
 * `config.yml` will be overwritten. Arrays will be replaced during merge,
 * not concatenated.
 *
 * For example, the code below contains an incomplete config, but using it,
 * your `config.yml` can be missing its backend property, allowing you
 * to set this property at runtime.
 */
init({
  config: {
    backend: {
      name: 'git-gateway',
    },
  },
})
/**
 * Optionally pass in a complete config object and set a flag
 *  (`load_config_file: false`) to ignore the `config.yml`.
 *
 * For example, the code below contains a complete config. The
 * `config.yml` will be ignored when setting `load_config_file` to false.
 * It is not required if the `config.yml` file is missing to set
 * `load_config_file`, but will improve performance and avoid a load error.
 */
init({
  config: {
    backend: {
      name: 'git-gateway',
    },
    load_config_file: false,
    media_folder: "static/images/uploads",
    public_folder: "/images/uploads",
    collections: [
      { label: "Blog", name: "blog", folder: "_posts/blog", create: true, fields: [
        { label: "Title", name: "title", widget: "string" },
        { label: "Publish Date", name: "date", widget: "datetime" },
        { label: "Featured Image", name: "thumbnail", widget: "image" },
        { label: "Body", name: "body", widget: "markdown" },
      ]},
    ],
  },
})
// The registry works as expected, and can be used before or after init.
CMS.registerPreviewTemplate(...);
```

### Raw CSS in `registerPreviewStyle` <a id="raw-css-in-registerpreviewstyle"></a>

`registerPreviewStyle` can now accept a CSS string, in addition to accepting a url. The feature is activated by passing in an object as the second argument, with `raw` set to a truthy value. This is critical for integrating with modern build tooling. Here's an example using webpack:

```text
/**
 * Assumes a webpack project with `sass-loader` and `css-loader` installed.
 * Takes advantage of the `toString` method in the return value of `css-loader`.
 */
import CMS from 'netlify-cms';
import styles from '!css-loader!sass-loader!../main.scss';
CMS.registerPreviewStyle(styles.toString(), { raw: true });
```

### Squash merge GitHub pull requests <a id="squash-merge-github-pull-requests"></a>

When using the [Editorial Workflow](https://www.netlifycms.org/docs/configuration-options/#publish-mode) with the `github` or GitHub-connected `git-gateway` backends, Netlify CMS creates a pull request for each unpublished entry. Every time the unpublished entry is changed and saved, a new commit is added to the pull request. When the entry is published, the pull request is merged, and all of those commits are added to your project commit history in a merge commit.

The squash merge option causes all commits to be "squashed" into a single commit when the pull request is merged, and the resulting commit is rebased onto the target branch, avoiding the merge commit altogether.

To enable this feature, you can set the following option in your Netlify CMS `config.yml`:

```text
backend:
  squash_merges: true
```

### Commit Message Templates <a id="commit-message-templates"></a>

You can customize the templates used by Netlify CMS to generate commit messages by setting the `commit_messages` option under `backend` in your Netlify CMS `config.yml`.

Template tags wrapped in curly braces will be expanded to include information about the file changed by the commit. For example, `{{path}}` will include the full path to the file changed.

Setting up your Netlify CMS `config.yml` to recreate the default values would look like this:

```text
backend:
  commit_messages:
    create: Create {{collection}} “{{slug}}”
    update: Update {{collection}} “{{slug}}”
    delete: Delete {{collection}} “{{slug}}”
    uploadMedia: Upload “{{path}}”
    deleteMedia: Delete “{{path}}”
    openAuthoring: '{{message}}'
```

Netlify CMS generates the following commit types:

| Commit type | When is it triggered? | Available template tags |
| :--- | :--- | :--- |
| `create` | A new entry is created | `slug`, `path`, `collection`, `author-login`, `author-name` |
| `update` | An existing entry is changed | `slug`, `path`, `collection`, `author-login`, `author-name` |
| `delete` | An existing entry is deleted | `slug`, `path`, `collection`, `author-login`, `author-name` |
| `uploadMedia` | A media file is uploaded | `path`, `author-login`, `author-name` |
| `deleteMedia` | A media file is deleted | `path`, `author-login`, `author-name` |
| `openAuthoring` | A commit is made via a forked repository | `message`, `author-login`, `author-name` |

Template tags produce the following output:

* `{{slug}}`: the url-safe filename of the entry changed
* `{{collection}}`: the name of the collection containing the entry changed
* `{{path}}`: the full path to the file changed
* `{{message}}`: the relevant message based on the current change \(e.g. the `create` message when an entry is created\)
* `{{author-login}}`: the login/username of the author
* `{{author-name}}`: the full name of the author \(might be empty based on the user's profile\)

### Image widget file size limit <a id="image-widget-file-size-limit"></a>

You can set a limit to as what the maximum file size of a file is that users can upload directly into a image field.

Example config:

```text
- label: 'Featured Image'
  name: 'thumbnail'
  widget: 'image'
  default: '/uploads/chocolate-dogecoin.jpg'
  media_library:
    config:
      max_file_size: 512000 # in bytes, only for default media library
```

### Summary string template transformations <a id="summary-string-template-transformations"></a>

You can apply transformations on fields in a summary string template using filter notation syntax.

Example config:

```text
collections:
  - name: 'posts'
    label: 'Posts'
    folder: '_posts'
    summary: "{{title | upper}} - {{date | date('YYYY-MM-DD')}}"
    fields:
      - { label: 'Title', name: 'title', widget: 'string' }
      - { label: 'Publish Date', name: 'date', widget: 'datetime' }
```

The above config will transform the title field to uppercase and format the date field using `YYYY-MM-DD` format. Available transformations are `upper`, `lower` and `date('<format>')`

### Registering to CMS Events <a id="registering-to-cms-events"></a>

You can execute a function when a specific CMS event occurs.

Example usage:

```text
CMS.registerEventListener({
  name: 'prePublish',
  handler: ({ author, entry }) => console.log(JSON.stringify({ author, data: entry.get('data') })),
});
```

Supported events are `prePublish`, `postPublish`, `preUnpublish`, `postUnpublish`, `preSave` and `postSave`. The `preSave` hook can be used to modify the entry data like so:

```text
CMS.registerEventListener({
  name: 'preSave',
  handler: ({ entry }) => {
    return entry.get('data').set('title', 'new title');
  },
});
```

### Dynamic Default Values <a id="dynamic-default-values"></a>

When linking to `/admin/#/collections/posts/new` you can pass URL parameters to pre-populate an entry.

For example given the configuration:

```text
collections:
  - name: posts
    label: Posts
    folder: content/posts
    create: true
    fields:
      - label: Title
        name: title
        widget: string
      - label: Object
        name: object
        widget: object
        fields:
          - label: Title
            name: title
            widget: string
      - label: body
        name: body
        widget: markdown
```

clicking the following link: `/#/collections/posts/new?title=first&object.title=second&body=%23%20content`

will open the editor for a new post with the `title` field populated with `first`, the nested `object.title` field with `second` and the markdown `body` field with `# content`.

**Note:** URL Encoding might be required for certain values \(e.g. in the previous example the value for `body` is URL encoded\).

### Nested Collections <a id="nested-collections"></a>

Allows a folder collection to show a nested structure of entries and edit the locations of the entries.

Example configuration:

```text
collections:
  - name: pages
    label: Pages
    label_singular: 'Page'
    folder: content/pages
    create: true
    # adding a nested object will show the collection folder structure
    nested:
      depth: 100 # max depth to show in the collection tree
      summary: '{{title}}' # optional summary for a tree node, defaults to the inferred title field
    fields:
      - label: Title
        name: title
        widget: string
      - label: Body
        name: body
        widget: markdown
    # adding a meta object with a path property allows editing the path of entries
    # moving an existing entry will move the entire sub tree of the entry to the new location
    meta: { path: { widget: string, label: 'Path', index_file: 'index' } }
```

Nested collections expect the following directory structure:

```text
content
└── pages
    ├── authors
    │   ├── author-1
    │   │   └── index.md
    │   └── index.md
    ├── index.md
    └── posts
        ├── hello-world
        │   └── index.md
        └── index.md
```

### Remark plugins <a id="remark-plugins"></a>

You can register plugins to customize [`remark`](https://github.com/remarkjs/remark), the library used by the richtext editor for serializing and deserializing markdown.

```text
// register a plugin
CMS.registerRemarkPlugin(plugin);

// provide global settings to all plugins, e.g. for customizing `remark-stringify`
CMS.registerRemarkPlugin({ settings: { bullet: '-' } });
```

Note that `netlify-widget-markdown` currently uses `remark@10`, so you should check a plugin's compatibility first.







![](.gitbook/assets/image.png)

{% page-ref page="navigation.md" %}

{% tabs %}
{% tab title="Important Links" %}

{% endtab %}

{% tab title="Website Nav" %}


## Table of contents

* [Home](https://bryan-guner.gitbook.io/web-dev-hub-docs/README)
* [Navigation](https://bryan-guner.gitbook.io/web-dev-hub-docs/navigation)

### Tools

* [Tools](https://bryan-guner.gitbook.io/web-dev-hub-docs/tools/tools/README)
  * [Heroku](https://bryan-guner.gitbook.io/web-dev-hub-docs/tools/tools/heroku)
* [Showcase](https://bryan-guner.gitbook.io/web-dev-hub-docs/tools/showcase)

### Programming Languages

* [Programming Languages](https://bryan-guner.gitbook.io/web-dev-hub-docs/programming-languages/programming-languages/README)
  * [Tools-Of-The-Trade](https://bryan-guner.gitbook.io/web-dev-hub-docs/programming-languages/programming-languages/frameworks/README)
    * [Docker](https://bryan-guner.gitbook.io/web-dev-hub-docs/programming-languages/programming-languages/frameworks/docker)
  * [Git](https://bryan-guner.gitbook.io/web-dev-hub-docs/programming-languages/programming-languages/git)
  * [Python](https://bryan-guner.gitbook.io/web-dev-hub-docs/programming-languages/programming-languages/python/README)
    * [Practice](https://bryan-guner.gitbook.io/web-dev-hub-docs/programming-languages/programming-languages/python/practice)
    * [Pipenv](https://bryan-guner.gitbook.io/web-dev-hub-docs/programming-languages/programming-languages/python/pipenv)
  * [Bash](https://bryan-guner.gitbook.io/web-dev-hub-docs/programming-languages/programming-languages/bash)
  * [JavaScript](https://bryan-guner.gitbook.io/web-dev-hub-docs/programming-languages/programming-languages/javascript/README)
    * [NodeJS](https://bryan-guner.gitbook.io/web-dev-hub-docs/programming-languages/programming-languages/javascript/nodejs/README)
      * [Express](https://bryan-guner.gitbook.io/web-dev-hub-docs/programming-languages/programming-languages/javascript/nodejs/express/README)
        * [Sequelize](https://bryan-guner.gitbook.io/web-dev-hub-docs/programming-languages/programming-languages/javascript/nodejs/express/sequelize)
    * [Regex](https://bryan-guner.gitbook.io/web-dev-hub-docs/programming-languages/programming-languages/javascript/regex)
    * [React](https://bryan-guner.gitbook.io/web-dev-hub-docs/programming-languages/programming-languages/javascript/react/README)
      * [Redux](https://bryan-guner.gitbook.io/web-dev-hub-docs/programming-languages/programming-languages/javascript/react/redux)
  * [Java](https://bryan-guner.gitbook.io/web-dev-hub-docs/programming-languages/programming-languages/java)
  * [Html](https://bryan-guner.gitbook.io/web-dev-hub-docs/programming-languages/programming-languages/html)
  * [Markdown](https://bryan-guner.gitbook.io/web-dev-hub-docs/programming-languages/programming-languages/markdown)
  * [CSS](https://bryan-guner.gitbook.io/web-dev-hub-docs/programming-languages/programming-languages/css)
  * [SCSS](https://bryan-guner.gitbook.io/web-dev-hub-docs/programming-languages/programming-languages/scss)
  * [C & C++](https://bryan-guner.gitbook.io/web-dev-hub-docs/programming-languages/programming-languages/c-and-c++)
  * [Ruby](https://bryan-guner.gitbook.io/web-dev-hub-docs/programming-languages/programming-languages/ruby)
  * [SQL](https://bryan-guner.gitbook.io/web-dev-hub-docs/programming-languages/programming-languages/sql/README)
    * [PostgreSQL](https://bryan-guner.gitbook.io/web-dev-hub-docs/programming-languages/programming-languages/sql/postgresql)
* [What is a Programming Language?](https://bryan-guner.gitbook.io/web-dev-hub-docs/programming-languages/what-is-a-programming-language)
* [List Of Programming Languages By Type](https://bryan-guner.gitbook.io/web-dev-hub-docs/programming-languages/list-of-programming-languages-by-type)

### Python

* [Python](https://bryan-guner.gitbook.io/web-dev-hub-docs/python/untitled)

### JavaScript

* [JavaScript](https://bryan-guner.gitbook.io/web-dev-hub-docs/javascript/untitled)

### Web Development Frameworks & Libraries <a id="web-development-frameworks"></a>

* [React](https://bryan-guner.gitbook.io/web-dev-hub-docs/web-development-frameworks/react)
* [Jquery](https://bryan-guner.gitbook.io/web-dev-hub-docs/web-development-frameworks/jquery)
* [GATSBY](https://bryan-guner.gitbook.io/web-dev-hub-docs/web-development-frameworks/gatsby/README)
  * [Building with Components](https://bryan-guner.gitbook.io/web-dev-hub-docs/web-development-frameworks/gatsby/building-with-components)
  * [Plugins, Themes, & Starters](https://bryan-guner.gitbook.io/web-dev-hub-docs/web-development-frameworks/gatsby/plugins-themes-and-starters)
  * [GraphQL Concepts](https://bryan-guner.gitbook.io/web-dev-hub-docs/web-development-frameworks/gatsby/graphql-concepts)

### Productivity

* [Productivity](https://bryan-guner.gitbook.io/web-dev-hub-docs/productivity/untitled/README)
  * [Awesome Productivity Tools for Back-to-School](https://bryan-guner.gitbook.io/web-dev-hub-docs/productivity/untitled/untitled)

### Misc

* [Misc](https://bryan-guner.gitbook.io/web-dev-hub-docs/misc/untitled)

### Links

* [Links](https://bryan-guner.gitbook.io/web-dev-hub-docs/links/untitled)
* [Bookmarks](https://bryan-guner.gitbook.io/web-dev-hub-docs/links/untitled-1)

### Websites

* [Websites](https://bryan-guner.gitbook.io/web-dev-hub-docs/websites/untitled)
* [Not My Websites:](https://bryan-guner.gitbook.io/web-dev-hub-docs/websites/untitled-1)

### Backend

* [Backend](https://bryan-guner.gitbook.io/web-dev-hub-docs/backend/untitled)

### Networking

* [Networks](https://bryan-guner.gitbook.io/web-dev-hub-docs/networking/untitled)

### Resources

* [Resources](https://bryan-guner.gitbook.io/web-dev-hub-docs/resources/untitled)
* [Video Resources](https://bryan-guner.gitbook.io/web-dev-hub-docs/resources/untitled-1)

### General Knowledge

* [General Knowledge](https://bryan-guner.gitbook.io/web-dev-hub-docs/general-knowledge/untitled)
* [Knowledge Bank](https://bryan-guner.gitbook.io/web-dev-hub-docs/general-knowledge/untitled-1)

### Finance

* [Finance](https://bryan-guner.gitbook.io/web-dev-hub-docs/finance/untitled)
* [Financial Trends](https://bryan-guner.gitbook.io/web-dev-hub-docs/finance/untitled-1)

### Science & Tech \(Innovation\)

* [Science & Tech](https://bryan-guner.gitbook.io/web-dev-hub-docs/science-and-tech-innovation/untitled)
* [Untitled](https://bryan-guner.gitbook.io/web-dev-hub-docs/science-and-tech-innovation/untitled-1)

### Reading

* [Reading](https://bryan-guner.gitbook.io/web-dev-hub-docs/reading/untitled)

### Social Media & Trends

* [Trends In Web Dev](https://bryan-guner.gitbook.io/web-dev-hub-docs/social-media-and-trends/untitled)

### Docs

* [Docs](https://bryan-guner.gitbook.io/web-dev-hub-docs/docs/docs)

### Code Editors & Tools

* [Vscode](https://bryan-guner.gitbook.io/web-dev-hub-docs/code-editors-and-tools/vscode/README)
  * [How To Speed Up Vscode](https://bryan-guner.gitbook.io/web-dev-hub-docs/code-editors-and-tools/vscode/how-to-speed-up-vscode)

### Cool Stuff

* [Cool Observable Notebooks](https://bryan-guner.gitbook.io/web-dev-hub-docs/cool-stuff/cool-observable-notebooks)

### Server-Side

* [GraphQL](https://bryan-guner.gitbook.io/web-dev-hub-docs/server-side/graphql/README)
  * [Intro](https://bryan-guner.gitbook.io/web-dev-hub-docs/server-side/graphql/intro)
* [REST-API](https://bryan-guner.gitbook.io/web-dev-hub-docs/server-side/rest-api)

### WEB\_DEV\_TOOLS

* [Cloudinary](https://bryan-guner.gitbook.io/web-dev-hub-docs/web_dev_tools/cloudinary)
* [Netlify](https://bryan-guner.gitbook.io/web-dev-hub-docs/web_dev_tools/netlify/README)
  * [Examples](https://bryan-guner.gitbook.io/web-dev-hub-docs/web_dev_tools/netlify/examples)
  * [Open Authoring](https://bryan-guner.gitbook.io/web-dev-hub-docs/web_dev_tools/netlify/open-authoring)
  * [Netlify CMS](https://bryan-guner.gitbook.io/web-dev-hub-docs/web_dev_tools/netlify/netlify-cms)
  * [Git Gateway](https://bryan-guner.gitbook.io/web-dev-hub-docs/web_dev_tools/netlify/git-gateway)
{% endtab %}
{% endtabs %}

## GIT REPO FOR THIS WEBSITE:

{% embed url="https://github.com/bgoonz/Web-Dev-Hub-Docs" %}

## Main Blog:

{% embed url="https://codepen.io/bgoonz/full/LYyBwEp" %}



{% embed url="https://bgoonz-blog.netlify.app/" %}

{% embed url="https://github.com/bgoonz/BGOONZ\_BLOG\_2.0" %}

{% embed url="https://codepen.io/bgoonz/full/OJgxqbm" caption="Website Navigation \(my websites\)" %}





## 

## Table of contents

* [Home](https://bryan-guner.gitbook.io/web-dev-hub-docs/README)
* [Navigation](https://bryan-guner.gitbook.io/web-dev-hub-docs/navigation)

### Tools

* [Tools](https://bryan-guner.gitbook.io/web-dev-hub-docs/tools/tools/README)
  * [Heroku](https://bryan-guner.gitbook.io/web-dev-hub-docs/tools/tools/heroku)
* [Showcase](https://bryan-guner.gitbook.io/web-dev-hub-docs/tools/showcase)

### Programming Languages

* [Programming Languages](https://bryan-guner.gitbook.io/web-dev-hub-docs/programming-languages/programming-languages/README)
  * [Frameworks](https://bryan-guner.gitbook.io/web-dev-hub-docs/programming-languages/programming-languages/frameworks/README)
    * [Docker](https://bryan-guner.gitbook.io/web-dev-hub-docs/programming-languages/programming-languages/frameworks/docker)
  * [Git](https://bryan-guner.gitbook.io/web-dev-hub-docs/programming-languages/programming-languages/git)
  * [Python](https://bryan-guner.gitbook.io/web-dev-hub-docs/programming-languages/programming-languages/python/README)
    * [Practice](https://bryan-guner.gitbook.io/web-dev-hub-docs/programming-languages/programming-languages/python/practice)
    * [Pipenv](https://bryan-guner.gitbook.io/web-dev-hub-docs/programming-languages/programming-languages/python/pipenv)
  * [Bash](https://bryan-guner.gitbook.io/web-dev-hub-docs/programming-languages/programming-languages/bash)
  * [JavaScript](https://bryan-guner.gitbook.io/web-dev-hub-docs/programming-languages/programming-languages/javascript/README)
    * [NodeJS](https://bryan-guner.gitbook.io/web-dev-hub-docs/programming-languages/programming-languages/javascript/nodejs/README)
      * [Express](https://bryan-guner.gitbook.io/web-dev-hub-docs/programming-languages/programming-languages/javascript/nodejs/express/README)
        * [Sequelize](https://bryan-guner.gitbook.io/web-dev-hub-docs/programming-languages/programming-languages/javascript/nodejs/express/sequelize)
    * [Regex](https://bryan-guner.gitbook.io/web-dev-hub-docs/programming-languages/programming-languages/javascript/regex)
    * [React](https://bryan-guner.gitbook.io/web-dev-hub-docs/programming-languages/programming-languages/javascript/react/README)
      * [Redux](https://bryan-guner.gitbook.io/web-dev-hub-docs/programming-languages/programming-languages/javascript/react/redux)
  * [Java](https://bryan-guner.gitbook.io/web-dev-hub-docs/programming-languages/programming-languages/java)
  * [Html](https://bryan-guner.gitbook.io/web-dev-hub-docs/programming-languages/programming-languages/html)
  * [Markdown](https://bryan-guner.gitbook.io/web-dev-hub-docs/programming-languages/programming-languages/markdown)
  * [CSS](https://bryan-guner.gitbook.io/web-dev-hub-docs/programming-languages/programming-languages/css)
  * [SCSS](https://bryan-guner.gitbook.io/web-dev-hub-docs/programming-languages/programming-languages/scss)
  * [C & C++](https://bryan-guner.gitbook.io/web-dev-hub-docs/programming-languages/programming-languages/c-and-c++)
  * [Ruby](https://bryan-guner.gitbook.io/web-dev-hub-docs/programming-languages/programming-languages/ruby)
  * [SQL](https://bryan-guner.gitbook.io/web-dev-hub-docs/programming-languages/programming-languages/sql/README)
    * [PostgreSQL](https://bryan-guner.gitbook.io/web-dev-hub-docs/programming-languages/programming-languages/sql/postgresql)
* [What is a Programming Language?](https://bryan-guner.gitbook.io/web-dev-hub-docs/programming-languages/what-is-a-programming-language)
* [List Of Programming Languages By Type](https://bryan-guner.gitbook.io/web-dev-hub-docs/programming-languages/list-of-programming-languages-by-type)

### Python

* [Untitled](https://bryan-guner.gitbook.io/web-dev-hub-docs/python/untitled)

### JavaScript

* [JavaScript](https://bryan-guner.gitbook.io/web-dev-hub-docs/javascript/untitled)

### Web Development Frameworks & Libraries <a id="web-development-frameworks"></a>

* [React](https://bryan-guner.gitbook.io/web-dev-hub-docs/web-development-frameworks/react)
* [Jquery](https://bryan-guner.gitbook.io/web-dev-hub-docs/web-development-frameworks/jquery)
* [GATSBY](https://bryan-guner.gitbook.io/web-dev-hub-docs/web-development-frameworks/gatsby/README)
  * [Building with Components](https://bryan-guner.gitbook.io/web-dev-hub-docs/web-development-frameworks/gatsby/building-with-components)
  * [Plugins, Themes, & Starters](https://bryan-guner.gitbook.io/web-dev-hub-docs/web-development-frameworks/gatsby/plugins-themes-and-starters)
  * [GraphQL Concepts](https://bryan-guner.gitbook.io/web-dev-hub-docs/web-development-frameworks/gatsby/graphql-concepts)

### Productivity

* [Untitled](https://bryan-guner.gitbook.io/web-dev-hub-docs/productivity/untitled)

### Misc

* [Untitled](https://bryan-guner.gitbook.io/web-dev-hub-docs/misc/untitled)

### Links

* [Links](https://bryan-guner.gitbook.io/web-dev-hub-docs/links/untitled)
* [Untitled](https://bryan-guner.gitbook.io/web-dev-hub-docs/links/untitled-1)

### Websites

* [Websites](https://bryan-guner.gitbook.io/web-dev-hub-docs/websites/untitled)
* [Untitled](https://bryan-guner.gitbook.io/web-dev-hub-docs/websites/untitled-1)

### Backend

* [Untitled](https://bryan-guner.gitbook.io/web-dev-hub-docs/backend/untitled)

### Networking

* [Untitled](https://bryan-guner.gitbook.io/web-dev-hub-docs/networking/untitled)

### Resources

* [Resources](https://bryan-guner.gitbook.io/web-dev-hub-docs/resources/untitled)
* [Untitled](https://bryan-guner.gitbook.io/web-dev-hub-docs/resources/untitled-1)

### General Knowledge

* [General Knowledge](https://bryan-guner.gitbook.io/web-dev-hub-docs/general-knowledge/untitled)
* [Untitled](https://bryan-guner.gitbook.io/web-dev-hub-docs/general-knowledge/untitled-1)

### Finance

* [Finance](https://bryan-guner.gitbook.io/web-dev-hub-docs/finance/untitled)
* [Untitled](https://bryan-guner.gitbook.io/web-dev-hub-docs/finance/untitled-1)

### Science & Tech \(Innovation\)

* [Science & Tech](https://bryan-guner.gitbook.io/web-dev-hub-docs/science-and-tech-innovation/untitled)
* [Untitled](https://bryan-guner.gitbook.io/web-dev-hub-docs/science-and-tech-innovation/untitled-1)

### Reading

* [Untitled](https://bryan-guner.gitbook.io/web-dev-hub-docs/reading/untitled)

### Social Media & Trends

* [Untitled](https://bryan-guner.gitbook.io/web-dev-hub-docs/social-media-and-trends/untitled)

### Docs

* [Docs](https://bryan-guner.gitbook.io/web-dev-hub-docs/docs/docs)

### Code Editors & Tools

* [Vscode](https://bryan-guner.gitbook.io/web-dev-hub-docs/code-editors-and-tools/vscode/README)
  * [How To Speed Up Vscode](https://bryan-guner.gitbook.io/web-dev-hub-docs/code-editors-and-tools/vscode/how-to-speed-up-vscode)

### Cool Stuff

* [Cool Observable Notebooks](https://bryan-guner.gitbook.io/web-dev-hub-docs/cool-stuff/cool-observable-notebooks)

### Server-Side

* [GraphQL](https://bryan-guner.gitbook.io/web-dev-hub-docs/server-side/graphql/README)
  * [Intro](https://bryan-guner.gitbook.io/web-dev-hub-docs/server-side/graphql/intro)
* [REST-API](https://bryan-guner.gitbook.io/web-dev-hub-docs/server-side/rest-api)

### WEB\_DEV\_TOOLS

* [Cloudinary](https://bryan-guner.gitbook.io/web-dev-hub-docs/web_dev_tools/cloudinary)
* [Netlify](https://bryan-guner.gitbook.io/web-dev-hub-docs/web_dev_tools/netlify/README)
  * [Examples](https://bryan-guner.gitbook.io/web-dev-hub-docs/web_dev_tools/netlify/examples)
  * [Open Authoring](https://bryan-guner.gitbook.io/web-dev-hub-docs/web_dev_tools/netlify/open-authoring)
  * [Netlify CMS](https://bryan-guner.gitbook.io/web-dev-hub-docs/web_dev_tools/netlify/netlify-cms)
  * [Git Gateway](https://bryan-guner.gitbook.io/web-dev-hub-docs/web_dev_tools/netlify/git-gateway)

![](.gitbook/assets/image%20%282%29%20%282%29%20%283%29%20%283%29%20%283%29%20%283%29%20%282%29.png)



**WEBSITE ⇄ Portfolio ⇄ Collaborate ⇄ Other-Websites ⇄ Recent Work**

[![Profile views](https://camo.githubusercontent.com/41fbd874c1ba0b69222440c1527ceb24831f388dcf4b875c048dbf0ce11fce58/68747470733a2f2f76696577732e77686174696c656172656e65642e746f6461792f76696577732f6769746875622f62676f6f6e7a2f76696577732e737667)](https://camo.githubusercontent.com/41fbd874c1ba0b69222440c1527ceb24831f388dcf4b875c048dbf0ce11fce58/68747470733a2f2f76696577732e77686174696c656172656e65642e746f6461792f76696577732f6769746875622f62676f6f6e7a2f76696577732e737667)[![Gitter](https://camo.githubusercontent.com/3b010aaec4938434c93c158f7ad38fd70693fdc8c1ec39baeadbebab61458138/68747470733a2f2f6261646765732e6769747465722e696d2f62676f6f6e7a2f636f6d6d756e6974792e737667)](https://gitter.im/bgoonz/community?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge)[![hackmd-github-sync-badge](https://camo.githubusercontent.com/c2a5c84d227b48f8b214bf813206875ac60e5f5a93ab8a22169e1b72e2764255/68747470733a2f2f6861636b6d642e696f2f354465596a326f585476474a312d587670314a6f32512f6261646765)](https://hackmd.io/5DeYj2oXTvGJ1-Xvp1Jo2Q)[![GitHub followers](https://camo.githubusercontent.com/c2e34390f77ac43483e3b319f52845547e27f20b2efe79455385c132962cf102/68747470733a2f2f696d672e736869656c64732e696f2f6769746875622f666f6c6c6f776572732f62676f6f6e7a2e7376673f7374796c653d736f6369616c266c6162656c3d466f6c6c6f77266d61784167653d32353932303030)](https://github.com/bgoonz?tab=followers)

#### ➤ _Email_ [bryan.guner@gmail.com](https://github.com/bgoonz#) _Phone_ [551-254-5505](https://github.com/bgoonz/bgoonz/blob/master/551-254-5505)

**A passionate Web Developer, Electrical Engineer, Musician & Producer**

![](https://camo.githubusercontent.com/727b46e1d3fa1dc9460d1f7a8c4f4fb8a5523029a3389abf818bc1f95430b4ac/68747470733a2f2f726561646d652d6a6f6b65732e76657263656c2e6170702f617069)

![code](https://github.com/bgoonz/bgoonz/raw/master/code.png?raw=true)

![statistics](https://github.com/bgoonz/bgoonz/raw/master/summary_50.png?raw=true)

[![Portfolio](https://camo.githubusercontent.com/ede4d8e5abd93426806a9e7bd3467603135f5ac527225f8cc01891948b48dd53/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f2d2545322539442541345f506f7274666f6c696f2d6635383f7374796c653d666c61742d737175617265266c6f676f3d61266c6f676f436f6c6f723d7768697465266c696e6b3d68747470733a2f2f62672d706f7274666f6c696f2e6e65746c6966792e6170702f)](https://bg-portfolio.netlify.app/) [![Resume PDF](https://camo.githubusercontent.com/be39e4efa075ffc9dec9f08ffa5296ab259a2190ca575bbc58881c2c79fc3447/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f2d526573756d652d6630303f7374796c653d666c61742d737175617265266c6f676f3d61646f62652d6163726f6261742d726561646572266c6f676f436f6c6f723d7768697465)](https://github.com/bgoonz/resume-cv-portfolio-samples/blob/master/2021-resume/bryan-guner-resume-2021.pdf) [![Bryan&apos;s email](https://camo.githubusercontent.com/917b3c0fed1a17b7658bc96dcae513d12e4bbf98e492342c33eb7b5089a7527f/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f627279616e2e67756e657240676d61696c2e636f6d2d6634623430303f7374796c653d666c61742d737175617265266c6f676f3d676d61696c266c6f676f436f6c6f723d626c61636b266c696e6b3d6d61696c746f3a627279616e2e67756e657240676d61696c2e636f6d)](mailto:bryan.guner@gmail.com) [![Blog](https://camo.githubusercontent.com/8e538703dad4c1dd05103d6a9aa6dfcecdd735439967d2501c172c1780bb3929/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f2d426c6f672d3231373539623f7374796c653d666c61742d737175617265266c6f676f3d576f72645072657373266c6f676f436f6c6f723d7768697465266c696e6b3d68747470733a2f2f7765622d6465762d6875622e636f6d2f)](https://web-dev-hub.com/) [![Linkedin](https://camo.githubusercontent.com/bc53eadd154132f3d3cc16188c2c49c98d07c60a59ffed1e42caa3f133afff12/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f2d4c696e6b6564496e2d3030373762353f7374796c653d666c61742d737175617265266c6f676f3d4c696e6b6564696e266c6f676f436f6c6f723d7768697465266c696e6b3d68747470733a2f2f7777772e6c696e6b6564696e2e636f6d2f696e2f627279616e2d67756e65722d3034363139393132382f)](https://www.linkedin.com/in/bryan-guner-046199128/) [![AngelList](https://camo.githubusercontent.com/c64472a38fcb4e49cb16b8026a44d74e8d9c5b0003796a282564ab8a40143d68/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f2d416e67656c4c6973742d626c61636b3f7374796c653d666c61742d737175617265266c6f676f3d416e67656c4c697374266c6f676f436f6c6f723d7768697465266c696e6b3d68747470733a2f2f616e67656c2e636f2f752f627279616e2d67756e6572)](https://angel.co/u/bryan-guner) [![GitHub bgoonz](https://camo.githubusercontent.com/32df1587a64824a89d979fe5b59a3e0bd166c44ced566dccf0d33ed62799157c/68747470733a2f2f696d672e736869656c64732e696f2f6769746875622f666f6c6c6f776572732f62676f6f6e7a3f6c6162656c3d666f6c6c6f77267374796c653d736f6369616c)](https://github.com/bgoonz)

[![email](https://camo.githubusercontent.com/987e9d7d7e70f98c5cd1d613add6ef56bcab60a8eda9e3df9f4f505b611ff773/68747470733a2f2f696d672e69636f6e73382e636f6d2f636f6c6f722f39362f3030303030302f676d61696c2e706e67)](mailto:bryan.guner@gmail.com)[![facebook](https://camo.githubusercontent.com/6acccefe72a9ad3380c0802e7a78988adad9d186eefff43b715bd7d7d07dc52a/68747470733a2f2f696d672e69636f6e73382e636f6d2f636f6c6f722f39362f3030303030302f66616365626f6f6b2e706e67)](https://www.facebook.com/bryan.guner/)[![twitter](https://camo.githubusercontent.com/a6a76173a04df8b3239debac70858c0d8bebd8d882b6572a0419cff3f51a2fc3/68747470733a2f2f696d672e69636f6e73382e636f6d2f636f6c6f722f39362f3030303030302f747769747465722d737175617265642e706e67)](https://twitter.com/bgooonz)[![youtube](https://camo.githubusercontent.com/aec347ccecfb57c504334b6723d26a419c1e7a871d467603d0a301272d5ac329/68747470733a2f2f696d672e69636f6e73382e636f6d2f636f6c6f722f39362f3030303030302f796f75747562652e706e67)](https://www.youtube.com/channel/UC9-rYyUMsnEBK8G8fCyrXXA/videos)[![instagram](https://camo.githubusercontent.com/13b4ab64e1a639ef039c1688b03c7a1a0aaa875a1858fa56888aa09c492aac6a/68747470733a2f2f696d672e69636f6e73382e636f6d2f636f6c6f722f39362f3030303030302f696e7374616772616d2d6e65772e706e67)](https://www.instagram.com/bgoonz/?hl=en)[![linkedin](https://camo.githubusercontent.com/4f660401d8469647f004f5740254c81a657f48d4c55a635be05ffb196c2be320/68747470733a2f2f696d672e69636f6e73382e636f6d2f636f6c6f722f39362f3030303030302f6c696e6b6564696e2e706e67)](https://www.linkedin.com/in/bryan-guner-046199128/) [![medium](https://camo.githubusercontent.com/a9c0792a5acd4b406effc40cd8b8c6575fad4a4062a37c5a1787e032f5a6192f/68747470733a2f2f696d672e69636f6e73382e636f6d2f636f6c6f722f39362f3030303030302f6d656469756d2d6c6f676f2e706e67)](https://bryanguner.medium.com/)[![spotify](https://camo.githubusercontent.com/936d3874f5be4f3e8b6dce7582d12d1425c9fc24dab49159180ee55c0dcba92a/68747470733a2f2f696d672e69636f6e73382e636f6d2f636f6c6f722f39362f3030303030302f73706f746966792d2d76312e706e67)](https://open.spotify.com/user/bgoonz?si=ShH9wYbIQWab5Jz_30BKFw)

![Bryans github activity graph](https://camo.githubusercontent.com/df85fe41e52c487dfc79d46086816010d0c40d54bbc03dfb5b1e89839bf27ec9/68747470733a2f2f61637469766974792d67726170682e6865726f6b756170702e636f6d2f67726170683f757365726e616d653d62676f6f6e7a26637573746f6d5f7469746c653d546869732532306973253230427279616e73253230416374697669747926686964655f626f726465723d74727565267468656d653d636861727472657573652d6461726b)

#### Languages and Tools:

[![arduino](https://camo.githubusercontent.com/b3a1cdd20d0f308634ddd4598cdaa729c2d77047f51e66fa7206b9b4bac94c23/68747470733a2f2f63646e2e776f726c64766563746f726c6f676f2e636f6d2f6c6f676f732f61726475696e6f2d312e737667) ](https://www.arduino.cc/)[![aws](https://raw.githubusercontent.com/devicons/devicon/master/icons/amazonwebservices/amazonwebservices-original-wordmark.svg) ](https://aws.amazon.com/)[![azure](https://camo.githubusercontent.com/6df31a460cb0c38f960e92812c8b6f8bce4c7f13170fb4782f0b31ab8e792ac2/68747470733a2f2f7777772e766563746f726c6f676f2e7a6f6e652f6c6f676f732f6d6963726f736f66745f617a7572652f6d6963726f736f66745f617a7572652d69636f6e2e737667) ](https://azure.microsoft.com/en-in/)[![babel](https://camo.githubusercontent.com/1abf71d00a4a13bfdeccdc131c65f02644fae4e746289bd7c21bf1d2af986389/68747470733a2f2f7777772e766563746f726c6f676f2e7a6f6e652f6c6f676f732f626162656c6a732f626162656c6a732d69636f6e2e737667) ](https://babeljs.io/)[![bash](https://camo.githubusercontent.com/bbb327d6ba7708520eaafd13396fed64d73bf5df5c4cdd0ba03cf0843f7a9340/68747470733a2f2f7777772e766563746f726c6f676f2e7a6f6e652f6c6f676f732f676e755f626173682f676e755f626173682d69636f6e2e737667) ](https://www.gnu.org/software/bash/)[![bootstrap](https://raw.githubusercontent.com/devicons/devicon/master/icons/bootstrap/bootstrap-plain-wordmark.svg) ](https://getbootstrap.com/)[![cplusplus](https://raw.githubusercontent.com/devicons/devicon/master/icons/cplusplus/cplusplus-original.svg) ](https://www.w3schools.com/cpp/)[![css3](https://raw.githubusercontent.com/devicons/devicon/master/icons/css3/css3-original-wordmark.svg) ](https://www.w3schools.com/css/)[![d3js](https://raw.githubusercontent.com/devicons/devicon/master/icons/d3js/d3js-original.svg) ](https://d3js.org/)[![docker](https://raw.githubusercontent.com/devicons/devicon/master/icons/docker/docker-original-wordmark.svg) ](https://www.docker.com/)[![elasticsearch](https://camo.githubusercontent.com/d4cbacdc000de378e0dcae3b5ee54923c0ad04f6e52b7aa886a748fba5578def/68747470733a2f2f7777772e766563746f726c6f676f2e7a6f6e652f6c6f676f732f656c61737469632f656c61737469632d69636f6e2e737667) ](https://www.elastic.co/)[![ember](https://raw.githubusercontent.com/devicons/devicon/master/icons/ember/ember-original-wordmark.svg) ](https://emberjs.com/)[![express](https://raw.githubusercontent.com/devicons/devicon/master/icons/express/express-original-wordmark.svg) ](https://expressjs.com/)[![firebase](https://camo.githubusercontent.com/dd4b2422ed3bfc9da88c43d18550375c66f9584327dff7ecc19315ce50b96f07/68747470733a2f2f7777772e766563746f726c6f676f2e7a6f6e652f6c6f676f732f66697265626173652f66697265626173652d69636f6e2e737667) ](https://firebase.google.com/)[![gatsby](https://camo.githubusercontent.com/ba9df1e4c5f7c9f6503f2668f03a934b4553c5840dd6067ee1ab013c2af86afc/68747470733a2f2f7777772e766563746f726c6f676f2e7a6f6e652f6c6f676f732f6761747362796a732f6761747362796a732d69636f6e2e737667) ](https://www.gatsbyjs.com/)[![gcp](https://camo.githubusercontent.com/582944f6627732531ce1a2e20ad43538d1896e16a5f159ea28fd137dbb8e798a/68747470733a2f2f7777772e766563746f726c6f676f2e7a6f6e652f6c6f676f732f676f6f676c655f636c6f75642f676f6f676c655f636c6f75642d69636f6e2e737667) ](https://cloud.google.com/)[![git](https://camo.githubusercontent.com/fbfcb9e3dc648adc93bef37c718db16c52f617ad055a26de6dc3c21865c3321d/68747470733a2f2f7777772e766563746f726c6f676f2e7a6f6e652f6c6f676f732f6769742d73636d2f6769742d73636d2d69636f6e2e737667) ](https://git-scm.com/)[![heroku](https://camo.githubusercontent.com/df12cb598044a3f38efc1f45e3580558c324cf8789b79487125044eeebcc4dee/68747470733a2f2f7777772e766563746f726c6f676f2e7a6f6e652f6c6f676f732f6865726f6b752f6865726f6b752d69636f6e2e737667) ](https://heroku.com/)[![html5](https://raw.githubusercontent.com/devicons/devicon/master/icons/html5/html5-original-wordmark.svg) ](https://www.w3.org/html/)[![javascript](https://raw.githubusercontent.com/devicons/devicon/master/icons/javascript/javascript-original.svg) ](https://developer.mozilla.org/en-US/docs/Web/JavaScript)[![jekyll](https://camo.githubusercontent.com/d7a4f32c1f2f389d3db9810abfe171cd5d68f9239d2918f975845faf0cbbb70c/68747470733a2f2f7777772e766563746f726c6f676f2e7a6f6e652f6c6f676f732f6a656b796c6c72622f6a656b796c6c72622d69636f6e2e737667) ](https://jekyllrb.com/)[![linux](https://raw.githubusercontent.com/devicons/devicon/master/icons/linux/linux-original.svg) ](https://www.linux.org/)[![mocha](https://camo.githubusercontent.com/4253eb6921d60a216772940978dea3a0cf2113f2f29b5545720d3b5b6960e467/68747470733a2f2f7777772e766563746f726c6f676f2e7a6f6e652f6c6f676f732f6d6f6368616a732f6d6f6368616a732d69636f6e2e737667) ](https://mochajs.org/)[![mongodb](https://raw.githubusercontent.com/devicons/devicon/master/icons/mongodb/mongodb-original-wordmark.svg) ](https://www.mongodb.com/)[![nextjs](https://camo.githubusercontent.com/300c2986a53648631c34798554da7cde3779de253b02a15da6bccc59ea9f0317/68747470733a2f2f63646e2e776f726c64766563746f726c6f676f2e636f6d2f6c6f676f732f6e6578746a732d332e737667) ](https://nextjs.org/)[![nodejs](https://raw.githubusercontent.com/devicons/devicon/master/icons/nodejs/nodejs-original-wordmark.svg) ](https://nodejs.org/)[![photoshop](https://raw.githubusercontent.com/devicons/devicon/master/icons/photoshop/photoshop-line.svg) ](https://www.photoshop.com/en)[ ](https://www.postgresql.org/)[![postman](https://camo.githubusercontent.com/93b32389bf746009ca2370de7fe06c3b5146f4c99d99df65994f9ced0ba41685/68747470733a2f2f7777772e766563746f726c6f676f2e7a6f6e652f6c6f676f732f676574706f73746d616e2f676574706f73746d616e2d69636f6e2e737667) ](https://postman.com/)[![pug](https://camo.githubusercontent.com/e6f31db76aa258d4e26be8464f2dff9796d5cf59185976df02dd80ae6a60cc9e/68747470733a2f2f63646e2e776f726c64766563746f726c6f676f2e636f6d2f6c6f676f732f7075672e737667) ](https://pugjs.org/)[![puppeteer](https://camo.githubusercontent.com/9391ab4ec8994e921292160bd31966a5886d8599af858ee5e513f2a3d868c403/68747470733a2f2f7777772e766563746f726c6f676f2e7a6f6e652f6c6f676f732f707074726465762f707074726465762d6f6666696369616c2e737667) ](https://github.com/puppeteer/puppeteer)[![python](https://raw.githubusercontent.com/devicons/devicon/master/icons/python/python-original.svg) ](https://www.python.org/)[![react](https://raw.githubusercontent.com/devicons/devicon/master/icons/react/react-original-wordmark.svg) ](https://reactjs.org/)[![reactnative](https://camo.githubusercontent.com/5c92eeb467fd5d2b1ef1c560e3c3c2f758a8d4e03a8136bda7b41a2d3d4a1b59/68747470733a2f2f72656163746e61746976652e6465762f696d672f6865616465725f6c6f676f2e737667) ](https://reactnative.dev/)[![typescript](https://raw.githubusercontent.com/devicons/devicon/master/icons/typescript/typescript-original.svg) ](https://www.typescriptlang.org/)[![vagrant](https://camo.githubusercontent.com/8ce24bc85eb4a64c6ad571675cbc7dca48a49feea462d572204505907c6ca957/68747470733a2f2f7777772e766563746f726c6f676f2e7a6f6e652f6c6f676f732f76616772616e7475702f76616772616e7475702d69636f6e2e737667) ](https://www.vagrantup.com/)[![webpack](https://raw.githubusercontent.com/devicons/devicon/d00d0969292a6569d45b06d3f350f463a0107b0d/icons/webpack/webpack-original-wordmark.svg) ](https://webpack.js.org/)[![xd](https://camo.githubusercontent.com/c205ecbe12500177d102169d97bc1c17c545155fdf5ec78c08d54ac53e5b38c1/68747470733a2f2f63646e2e776f726c64766563746f726c6f676f2e636f6d2f6c6f676f732f61646f62652d78642e737667) ](https://www.adobe.com/products/xd.html)[![zapier](https://camo.githubusercontent.com/8f391d66440860f43c55d4d8ad22733a6919059108642cfdad9be4fc5f4f1d79/68747470733a2f2f7777772e766563746f726c6f676f2e7a6f6e652f6c6f676f732f7a61706965722f7a61706965722d69636f6e2e737667)](https://zapier.com/)

![postgresql](https://raw.githubusercontent.com/devicons/devicon/master/icons/postgresql/postgresql-original-wordmark.svg)

[![bgoonz](https://camo.githubusercontent.com/3a0b40daebc9fe1d25f51ff4923c0d02451e3f986c04d95692e919412afc3f95/68747470733a2f2f6769746875622d726561646d652d73747265616b2d73746174732e6865726f6b756170702e636f6d2f3f757365723d62676f6f6e7a26)](https://camo.githubusercontent.com/3a0b40daebc9fe1d25f51ff4923c0d02451e3f986c04d95692e919412afc3f95/68747470733a2f2f6769746875622d726561646d652d73747265616b2d73746174732e6865726f6b756170702e636f6d2f3f757365723d62676f6f6e7a26)[![bgoonz](https://camo.githubusercontent.com/cf5d4a973533a4c4231a2385ee68163bc88bc95bcc047eb238ee8269943a60b6/68747470733a2f2f6769746875622d726561646d652d73746174732e76657263656c2e6170702f6170693f757365726e616d653d62676f6f6e7a2673686f775f69636f6e733d74727565266c6f63616c653d656e)](https://camo.githubusercontent.com/cf5d4a973533a4c4231a2385ee68163bc88bc95bcc047eb238ee8269943a60b6/68747470733a2f2f6769746875622d726561646d652d73746174732e76657263656c2e6170702f6170693f757365726e616d653d62676f6f6e7a2673686f775f69636f6e733d74727565266c6f63616c653d656e)

| [GitHub](https://github.com/bgoonz) | [Gitlab](https://gitlab.com/bryan.guner.dev) | [Bitbucket](https://bitbucket.org/bgoonz/) | [Medium](https://bryanguner.medium.com/) | [code pen](https://codepen.io/bgoonz) |
| :--- | :--- | :--- | :--- | :--- |
| [Replit](https://repl.it/@bgoonz/) | [Quora](https://www.quora.com/q/webdevresourcehub?invite_code=qwZOqbpAhgQ6hjjGl8NN) | [Redit](https://www.reddit.com/user/bgoonz1) | [webcomponents.dev](https://webcomponents.dev/user/bgoonz) | [dev.to](https://dev.to/bgoonz) |
| [runkit](https://runkit.com/bgoonz) | [Observable Notebooks](https://observablehq.com/@bgoonz?tab=profile) | [npm](https://www.npmjs.com/~bgoonz11) | [stack-exchange](https://meta.stackexchange.com/users/936785/bryan-guner) | [Observable Notebooks](https://observablehq.com/@bgoonz?tab=profile) |
| [Upwork](https://www.upwork.com/freelancers/~01bb1a3627e1e9c630?viewMode=1&s=1110580755057594368) | [Notion](https://www.notion.so/Overview-Of-Css-5d88b0bc9a73422a9be1481d599a56ba) | [AngelList](https://angel.co/u/bryan-guner) | [StackShare](https://stackshare.io/bryanguner) | [Plunk](http://plnkr.co/account/plunks) |
| [giphy](https://giphy.com/channel/bryanguner) | [kofi](https://ko-fi.com/bgoonz) | [Codewars](https://www.codewars.com/users/bgoonz) | [Dribble](https://dribbble.com/bgoonz4242?onboarding=true) | [Glitch](https://glitch.com/@bgoonz) |
| [contentful](https://app.contentful.com/spaces/lelpu0ihaz11/assets?id=MocOPmmNliLn6PPv) | [Netlify](https://app.netlify.com/user/settings#profile) | [Stackblitz](https://stackblitz.com/@bgoonz) | [Vercel](https://vercel.com/bgoonz) | [Youtube](https://www.youtube.com/channel/UC9-rYyUMsnEBK8G8fCyrXXA/featured) |
| [wordpress](https://web-dev-hub.com/) | [Edabit](https://edabit.com/user/dsRcx6yCwAgYwZbRB) | [Vinmeo](https://vimeo.com/user128661018) | [js fiddle](https://jsfiddle.net/user/bgoonz/) | [hashnode](https://hashnode.com/@bgoonz/joinme) |
| [Google Developer Profile](https://developers.google.com/profile/u/100803355943326309646?utm_source=developers.google.com) | [Gittee](https://gitee.com/bgoonz) | [Wakatime](https://wakatime.com/@bgoonz42) | [Hubpages](https://hubpages.com/@bryanguner) | [Free Code Camp](https://www.freecodecamp.org/bgoonz) |

[![ReadMe Card](https://camo.githubusercontent.com/19e0a9e7039e5b0cfda7c36d7cae8b212758ad4435983ec2b0ea0bd4dccb153b/68747470733a2f2f6769746875622d726561646d652d73746174732e76657263656c2e6170702f6170692f70696e2f3f757365726e616d653d62676f6f6e7a267265706f3d4c616d626461)](https://github.com/bgoonz/Lambda) [![ReadMe Card](https://camo.githubusercontent.com/083e5f58841eeac90d6d98bde702aebf767f84f4734480c69b328920a901e863/68747470733a2f2f6769746875622d726561646d652d73746174732e76657263656c2e6170702f6170692f70696e2f3f757365726e616d653d62676f6f6e7a267265706f3d44532d414c474f2d4f4646494349414c)](https://github.com/bgoonz/DS-ALGO-OFFICIAL) [![ReadMe Card](https://camo.githubusercontent.com/c8b96a2b22ddc2ef31acba921d0317e3e37dc1433fd384b18b6cab7dcf4d1dff/68747470733a2f2f6769746875622d726561646d652d73746174732e76657263656c2e6170702f6170692f70696e2f3f757365726e616d653d62676f6f6e7a267265706f3d42474f4f4e5a5f424c4f475f322e30)](https://github.com/bgoonz/BGOONZ_BLOG_2.0)[![ReadMe Card](https://camo.githubusercontent.com/2863e7bc53a33d6fe810d97bbd699d186ade3f1b73841fe03f71f62af19de825/68747470733a2f2f6769746875622d726561646d652d73746174732e76657263656c2e6170702f6170692f70696e2f3f757365726e616d653d62676f6f6e7a267265706f3d444154415f53545255435f505954484f4e5f4e4f544553)](https://github.com/bgoonz/DATA_STRUC_PYTHON_NOTES)

![trophy](https://camo.githubusercontent.com/ec5908a2b7b26de2d103e5281abfb6ca1d1a31b3b9b6e54c1e72c01c123e995f/68747470733a2f2f6769746875622d70726f66696c652d74726f7068792e76657263656c2e6170702f3f757365726e616d653d62676f6f6e7a26726f773d31)

![Top Langs](https://camo.githubusercontent.com/f792848d7595c91467c42491e4fc91d745e4feed3810fc941829f73ed21dd40d/68747470733a2f2f6769746875622d726561646d652d73746174732e76657263656c2e6170702f6170692f746f702d6c616e67732f3f757365726e616d653d62676f6f6e7a266c61796f75743d636f6d7061637426686964653d68746d6c2c6d617468656d6174696361266c616e67735f636f756e743d3136)

![](.gitbook/assets/image%20%282%29%20%282%29%20%283%29%20%283%29%20%283%29%20%283%29%20%281%29.png)

## Python Website:

{% embed url="https://py-v2.gitbook.io/datastructures-in-pytho/" %}

{% embed url="https://github.com/bgoonz/python-gitbook" %}



## Widgets

Widgets define the data type and interface for entry fields. Netlify CMS comes with several built-in widgets. Click the widget names in the sidebar to jump to specific widget details. We’re always adding new widgets, and you can also [create your own](https://www.netlifycms.org/docs/custom-widgets)!

Widgets are specified as collection fields in the Netlify CMS `config.yml` file. Note that [YAML syntax](https://en.wikipedia.org/wiki/YAML#Basic_components) allows lists and objects to be written in block or inline style, and the code samples below include a mix of both.

To see working examples of all of the built-in widgets, try making a 'Kitchen Sink' collection item on the [CMS demo site](https://cms-demo.netlify.com/). \(No login required: click the login button and the CMS will open.\) You can refer to the demo [configuration code](https://github.com/netlify/netlify-cms/blob/master/dev-test/config.yml) to see how each field was configured.

### Common widget options <a id="common-widget-options"></a>

The following options are available on all fields:

* `required`: specify as `false` to make a field optional; defaults to `true`
* `hint`: optionally add helper text directly below a widget. Useful for including instructions. Accepts markdown for bold, italic, strikethrough, and links.
* `pattern`: add field validation by specifying a list with a [regex pattern](https://regexr.com/) and an error message; more extensive validation can be achieved with [custom widgets](https://www.netlifycms.org/docs/custom-widgets/#advanced-field-validation)
  * **Example:** `yaml - label: "Title" name: "title" widget: "string" pattern: ['.{12,}', "Must have at least 12 characters"]`

### Default widgets <a id="default-widgets"></a>

BooleanCodeColorDateDateTimeFileHiddenImageListMapMarkdownNumberObjectRelationSelectStringText

#### Boolean

The boolean widget translates a toggle switch input to a true/false value.

* **Name:** `boolean`
* **UI:** toggle switch
* **Data type:** boolean
* **Options:**
  * `default`: accepts `true` or `false`; defaults to `false` when `required` is set to `false`
* **Example:**

  ```text
  - {label: "Draft", name: "draft", widget: "boolean", default: true}
  ```

