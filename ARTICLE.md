# Build a blog with Next (React.js) and Strapi

Learn to create a blog website using Strapi for the backend and Next.js for the frontend.

## Introduction

[Strapi is the leading open-source composable Content Management System (CMS)](https://strapi.io). It is 100% Javascript, based on Node.js, and used to build RESTful APIs and GraphQL APIs. It also has a cloud SAAS, ☁ [Strapi Cloud](https://strapi.io/cloud) ☁ - a fully managed, composable, and collaborative platform to boost your content velocity!

Strapi enables developers to build projects faster with a flexible and customizable platform for managing content. Check out the [Strapi Quickstart](https://docs.strapi.io/dev-docs/quick-start) guide for a brief intro.

[The Strapi Team made a New Strapi Starter](https://strapi.io/blog/introducing-the-new-strapi-starter-with-nextjs13-tailwind-and-typescript) using Next.js 13, Tailwind, and TypeScript. In this tutorial, you will learn to create a blog website based off of the new Strapi Starter. The blog will use Strapi for the backend and Next.js 13 for the frontend.

## Before Getting Started

Here's the [final result of the blog website]() you will build if you are curious to see it. Check out the [project repo here]().

## Prerequisites

- This tutorial uses Strapi `v4.10.x`
- Node `v16.x.x` or `v18.x.x`. You can [download Node.js here.]()
- yarn `v1.22.x`. You can download [yarn here.]()
- Familiarity with Linux shell.

## Step 1: Setup project folder

Open up your terminal and create a folder named `blog-strapi` to store your project files.

```bash
mkdir blog-strapi
```

Navigate into `blog-strapi`.

```bash
cd blog-strapi
```

## Step 2: Create a standard Strapi app

Create your Strapi app in a folder named `backend`.

```bash
yarn create strapi-app backend --quickstart
```

The `--quickstart` flag sets up your Strapi app with an [SQLite](https://www.sqlite.org/index.html) database and automatically starts your server on port `1337`.

> **TIP**
>
> If the server is not already running, in your terminal, `cd` into the `backend` folder and run `yarn develop` to launch it.

Visit `http://localhost:1337/admin` in your browser and register your details in the Strapi Admin Registration Form.

![Strapi Admin Registration Form]()

After registering your admin user, you should see the `Strapi Dashboard` in your browser.

![Strapi Admin Dashboard]()

### Step 3: Seeding The Data

Next, you will seed your Strapi app database with data to use for the blog post. This includes articles, authors, images, and titles. The [Data import](https://docs.strapi.io/dev-docs/data-management/import) feature is quite handy in that regard.

Stop your Strapi backend server by pressing `CTRL` plus `C` on your keyboard then import data into your backend's database.

```bash
^C
yarn import https://github.com/strapi/nextjs-corporate-starter/raw/main/seed-data.tar.gz
```

> **NOTE:**
>
> The data used to seed the database is from the Strapi Nextjs Starter

Rerun your Strapi backend server and login to your admin panel. You should see the newly imported `content` and `collection types`.