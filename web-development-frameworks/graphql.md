# GRAPHQL

## Gatsby

This guide will help you get started using Netlify CMS and Gatsby.

To get up and running with Gatsby, you’ll need to have [Node.js](https://nodejs.org/) installed on your computer. _Note: Gatsby's minimum supported Node.js version is Node 8._

### Create a new Gatsby site <a id="create-a-new-gatsby-site"></a>

Let's create a new site using the default Gatsby Starter Blog. Run the following commands in the terminal, in the folder where you'd like to create the blog:

```text
npm install -g gatsby-cli
gatsby new blog https://github.com/gatsbyjs/gatsby-starter-blog
cd blog
```

### Get to know Gatsby <a id="get-to-know-gatsby"></a>

In your favorite code editor, open up the code generated for your "Gatsby Starter Blog" site, and take a look at the `content` directory.

You will see that there are multiple Markdown files that represent blog posts. Open one `.md` file and you will see something like this:

```text
---
title: New Beginnings
date: "2015-05-28T22:40:32.169Z"
description: This is an optional description for SEO and Open Graph purposes, rather than the default generated excerpt.
---

Far far away, behind the word mountains, far from the countries Vokalia and
Consonantia, there live the blind texts.
```

We can see above that each blog post has a title, a date, a description and a body. Now, let's recreate this using Netlify CMS.

### Add Netlify CMS to your site <a id="add-netlify-cms-to-your-site"></a>

First let's install some dependencies. We'll need `netlify-cms-app` and `gatsby-plugin-netlify-cms`. Run the following command in the terminal at the root of your site:

```text
npm install --save netlify-cms-app gatsby-plugin-netlify-cms
```

#### Configuration <a id="configuration"></a>

For the purpose of this guide we will deploy to Netlify from a GitHub repository which requires the minimum configuration.

Create a `config.yml` file in the directory structure you see below:

```text
├── static
│   ├── admin
│   │   ├── config.yml
```

In your `config.yml` file paste the following configuration:

```text
backend:
  name: git-gateway
  branch: main # Branch to update (optional; defaults to master)

media_folder: static/img
public_folder: /img

collections:
  - name: 'blog'
    label: 'Blog'
    folder: 'content/blog'
    create: true
    slug: 'index'
    media_folder: ''
    public_folder: ''
    path: '{{title}}/index'
    editor:
      preview: false
    fields:
      - { label: 'Title', name: 'title', widget: 'string' }
      - { label: 'Publish Date', name: 'date', widget: 'datetime' }
      - { label: 'Description', name: 'description', widget: 'string' }
      - { label: 'Body', name: 'body', widget: 'markdown' }
```

**Note:** The above configuration allows assets to be stored relative to their content. Therefore posts would be stored in the format below as it is in `gatsby-starter-blog`.

```text
content/
├── blog
│   ├── first-post-title
│   │   ├── index.md
│   │   └── post-image.jpg
└── └── second-post-title
        ├── index.md
        └── post-image.jpg
```

Finally, add the plugin to your `gatsby-config.js`.

```text
plugins: [`gatsby-plugin-netlify-cms`]
```

#### Push to GitHub <a id="push-to-github"></a>

It's now time to commit your changes and push to GitHub. The Gatsby starter initializes Git automatically for you, so you only need to do:

```text
git add .
git commit -m "Initial Commit"
git remote add origin https://github.com/YOUR_USERNAME/NEW_REPO_NAME.git
git push -u origin main
```

#### Add your repo to Netlify <a id="add-your-repo-to-netlify"></a>

Go to Netlify and select 'New Site from Git'. Select GitHub and the repository you just pushed to. Click Configure Netlify on GitHub and give access to your repository. Finish the setup by clicking Deploy Site. Netlify will begin reading your repository and starting building your project.

#### Enable Identity and Git Gateway <a id="enable-identity-and-git-gateway"></a>

Netlify's Identity and Git Gateway services allow you to manage CMS admin users for your site without requiring them to have an account with your Git host or commit access on your repo. From your site dashboard on Netlify:

1. Go to **Settings &gt; Identity**, and select **Enable Identity service**.
2. Under **Registration preferences**, select **Open** or **Invite only**. In most cases, you want only invited users to access your CMS, but if you're just experimenting, you can leave it open for convenience.
3. If you'd like to allow one-click login with services like Google and GitHub, check the boxes next to the services you'd like to use, under **External providers**.
4. Scroll down to **Services &gt; Git Gateway**, and click **Enable Git Gateway**. This authenticates with your Git host and generates an API access token. In this case, we're leaving the **Roles** field blank, which means any logged in user may access the CMS. For information on changing this, check the [Netlify Identity documentation](https://www.netlify.com/docs/identity/).

### Start publishing <a id="start-publishing"></a>

It's time to create your first blog post. Login to your site's `/admin/` page and create a new post by clicking New Blog. Add a title, a date and some text. When you click Publish, a new commit will be created in your GitHub repo with this format `Create Blog “year-month-date-title”`.

Then Netlify will detect that there was a commit in your repo, and will start rebuilding your project. When your project is deployed you'll be able to see the post you created.

#### Cleanup <a id="cleanup"></a>

It is now safe to remove the default Gatsby blog posts.

## About the GraphQL API

The GitHub GraphQL API offers flexibility and the ability to define precisely the data you want to fetch.

### [In this article](https://docs.github.com/en/graphql/overview/about-the-graphql-api#in-this-article) <a id="in-this-article"></a>

* [Overview](https://docs.github.com/en/graphql/overview/about-the-graphql-api#overview)
* [About GraphQL](https://docs.github.com/en/graphql/overview/about-the-graphql-api#about-graphql)
* [Why GitHub is using GraphQL](https://docs.github.com/en/graphql/overview/about-the-graphql-api#why-github-is-using-graphql)
* [About the GraphQL schema reference](https://docs.github.com/en/graphql/overview/about-the-graphql-api#about-the-graphql-schema-reference)
* [Requesting support](https://docs.github.com/en/graphql/overview/about-the-graphql-api#requesting-support)

### Overview <a id="overview"></a>

Here are some quick links to get you up and running with the GraphQL API v4:

* [Authentication](https://docs.github.com/en/graphql/guides/forming-calls-with-graphql#authenticating-with-graphql)
* [Root endpoint](https://docs.github.com/en/graphql/guides/forming-calls-with-graphql#the-graphql-endpoint)
* [Schema introspection](https://docs.github.com/en/graphql/guides/introduction-to-graphql#discovering-the-graphql-api)
* [Rate limits](https://docs.github.com/en/graphql/overview/resource-limitations)
* [Migrating from REST](https://docs.github.com/en/graphql/guides/migrating-from-rest-to-graphql)

### About GraphQL <a id="about-graphql"></a>

The [GraphQL](https://graphql.github.io/) data query language is:

* **A** [**specification**](https://graphql.github.io/graphql-spec/June2018/)**.** The spec determines the validity of the [schema](https://docs.github.com/en/graphql/guides/introduction-to-graphql#schema) on the API server. The schema determines the validity of client calls.
* [**Strongly typed**](https://docs.github.com/en/graphql/overview/about-the-graphql-api#about-the-graphql-schema-reference)**.** The schema defines an API's type system and all object relationships.
* [**Introspective**](https://docs.github.com/en/graphql/guides/introduction-to-graphql#discovering-the-graphql-api)**.** A client can query the schema for details about the schema.
* [**Hierarchical**](https://docs.github.com/en/graphql/guides/forming-calls-with-graphql)**.** The shape of a GraphQL call mirrors the shape of the JSON data it returns. [Nested fields](https://docs.github.com/en/graphql/guides/migrating-from-rest-to-graphql#example-nesting) let you query for and receive only the data you specify in a single round trip.
* **An application layer.** GraphQL is not a storage model or a database query language. The _graph_ refers to graph structures defined in the schema, where [nodes](https://docs.github.com/en/graphql/guides/introduction-to-graphql#node) define objects and [edges](https://docs.github.com/en/graphql/guides/introduction-to-graphql#edge) define relationships between objects. The API traverses and returns application data based on the schema definitions, independent of how the data is stored.

### Why GitHub is using GraphQL <a id="why-github-is-using-graphql"></a>

GitHub chose GraphQL for our API v4 because it offers significantly more flexibility for our integrators. The ability to define precisely the data you want—and _only_ the data you want—is a powerful advantage over the REST API v3 endpoints. GraphQL lets you replace multiple REST requests with _a single call_ to fetch the data you specify.

For more details about why GitHub has moved to GraphQL, see the original [announcement blog post](https://githubengineering.com/the-github-graphql-api/).

### About the GraphQL schema reference <a id="about-the-graphql-schema-reference"></a>

The docs in the sidebar are generated from the GitHub GraphQL [schema](https://docs.github.com/en/graphql/guides/introduction-to-graphql#discovering-the-graphql-api). All calls are validated and executed against the schema. Use these docs to find out what data you can call:

* Allowed operations: [queries](https://docs.github.com/en/graphql/reference/queries) and [mutations](https://docs.github.com/en/graphql/reference/mutations).
* Schema-defined types: [scalars](https://docs.github.com/en/graphql/reference/scalars), [objects](https://docs.github.com/en/graphql/reference/objects), [enums](https://docs.github.com/en/graphql/reference/enums), [interfaces](https://docs.github.com/en/graphql/reference/interfaces), [unions](https://docs.github.com/en/graphql/reference/unions), and [input objects](https://docs.github.com/en/graphql/reference/input-objects).

You can access this same content via the [Explorer Docs sidebar](https://docs.github.com/en/graphql/guides/using-the-explorer#accessing-the-sidebar-docs). Note that you may need to rely on both the docs and the schema validation to successfully call the GraphQL API.

For other information, such as authentication and rate limit details, check out the [guides](https://docs.github.com/en/graphql/guides).

### Requesting support <a id="requesting-support"></a>

For questions, bug reports, and discussions about GitHub Apps, OAuth Apps, and API development, explore the [GitHub API Development and Support Forum](https://github.community/c/github-api-development-and-support/37). The forum is moderated and maintained by GitHub staff, but questions posted to the forum are not guaranteed to receive a reply from GitHub staff.

Consider reaching out to [GitHub Support](https://github.com/contact) directly using the contact form for:

* guaranteed response from GitHub staff
* support requests involving sensitive data or private concerns
* feature requests
* feedback about GitHub products

