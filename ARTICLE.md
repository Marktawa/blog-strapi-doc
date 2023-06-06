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

## Step 3: Seeding The Data

Next, you will seed your Strapi app database with data to use for the blog post. This includes articles, authors, images, categories, and so on. The [Data import](https://docs.strapi.io/dev-docs/data-management/import) feature is quite handy in that regard.

Go back to your terminal and stop your Strapi backend server by pressing `CTRL` plus `C` on your keyboard. 

Update the schema for your Strapi app so that the data won't be lost when you perform a data import. Make sure you are executing the command in the root of your project folder `blog-strapi`.

```bash
wget https://raw.githubusercontent.com/Marktawa/blog-strapi-archives/main/seed-schema.tar
tar xvf seed-schema.tar -C backend
```

Next, import data into your backend's database.

```bash
wget https://raw.githubusercontent.com/strapi/nextjs-corporate-starter/main/seed-data.tar.gz
cd backend
yarn strapi import -f ../seed-data.tar.gz
```

Answer `y` to `The import will delete all assets and data in your database. Are you sure you want to proceed? (y/N)`

> **NOTE:**
>
> The data used to seed the database is from the Strapi Nextjs Starter.

After a successful import, rerun your Strapi backend server and login to your admin panel. You should see the newly imported `content` and `collection types`.

![Updated Dashboard]()

## Step 4: Create a standard Next.js app

In a new terminal session, change directory to `blog-strapi` and run the following command:

```bash
yarn create next-app
```

On installation, you'll see some prompts. Name your project `frontend` and refer to the image below for the other responses.

![Create Next App Prompts]()

Add the following dependencies to your `frontend` Next app: `@types/negotiator`, `negotiator`, `@types/qs`, `qs`, `classnames`, `react-icons`, `react-markdown`, `react-slideshow-image`, `remark-gfm`, `@formatjs/intl-localematcher` for use later.

```bash
cd frontend
yarn add @types/negotiator negotiator @types/qs qs classnames react-icons react-markdown react-slideshow-image remark-gfm @formatjs/intl-localematcher
```

Move the following dependencies to devdependencies in your `frontend` Next app: `autoprefixer`, `postcss`, `tailwindcss`.

```bash
yarn remove autoprefixer postcss tailwindcss
yarn add autoprefixer postcss tailwindcss --dev
```

## Step 5: Set up Strapi API Tokens

Create a `.env` file in the root of your `frontend` directory and paste the following environment variables:

```
NEXT_PUBLIC_STRAPI_API_TOKEN=your-api-token
NEXT_PUBLIC_PAGE_LIMIT=6
NEXT_PUBLIC_STRAPI_FORM_SUBMISSION_TOKEN=your-form-submission-token
NEXT_PUBLIC_STRAPI_API_URL=http://localhost:1337
```

Go back to your Strapi Admin panel to create the API tokens to be used for displaying content and form submission.

Inside your Strapi Admin Panel, click on `Settings`, select `API Tokens` and click on the `+ Create new API Token` button.

![Create new API Token]()

Configure your API Token with the following details:

- Name: `Public API Token Content`
- Description: `Access to public content`
- Token duration: `Unlimited`
- Token type: `Custom`

![Public API Token Content Details]()

Configure your API Token with the following permissions:

| Content | Permissions |
|---------|-------------|
| Article | find and findOne|
| Author | find and findOne|
| Category | find and findOne|
| Global | find |
| Page | find and findOne|
| Product-feature | find and findOne|

![Public API Token Content Permissions](https://user-images.githubusercontent.com/6153188/231865625-a3634d89-0f40-4a6d-a356-8f654abd88b9.gif)

Click on `Save` then copy the generated API token and paste it next to the `NEXT_PUBLIC_STRAPI_API_TOKEN` in your `.env` file.

> **NOTE:**
>
> The generated API token is only visible once, so make sure to copy it right after you generate one.

Create one more API Token for form submission. Configure the Form Submission API Token with the following details:

- Name: `Public API Form Submit`
- Description: `Form Submission`
- Token duration: `Unlimited`
- Token type: `Custom`

![Public API Form Submit Details]()

Configure the Form Submission API Token with the following permissions:

| Content | Permissions |
|---------|-------------|
| Lead-form-submission | create|

![Public API Form Submit Permissions]()

Click on `Save` then copy the generated API token and paste it next to the `NEXT_PUBLIC_STRAPI_FORM_SUBMISSION_TOKEN` in your `.env` file.

Your updated `.env` file should have environment variables similar to this:

```
NEXT_PUBLIC_STRAPI_API_TOKEN=ab2bfc3f905e61b58cbb76c1d459609ac29351249f5ffb98db9dd6d109ab60d651de15e0d31e3e367a754e622fe3c918fef89f0c28ae7b9c6e788d3db4d2ea7abd7b282b37b8448886c865607965ea82c57648e11de6bb278c7bbac811051287dbf6657cac0604716fd4d4501771db43b36b531b8418ca5c8a4bc248eb85b266
NEXT_PUBLIC_PAGE_LIMIT=6
NEXT_PUBLIC_STRAPI_FORM_SUBMISSION_TOKEN=32d7d6ba5e71598eb8c85dc41f1cfaf4119f2756ff995be6960265bb2a8128bc8ea06cb0157b4ec56380991d3d1c9057e218a9f99a75b84cd0bcc9b7cc937539a4b1960cea08376c2a0218f32892044652e70235bc052c44b6e7e8407a10ac0cb61a73e4da593915c7c28d37319ac4af18c4fb0a2ab38669495d4c1f0f0b53b1
NEXT_PUBLIC_STRAPI_API_URL=http://localhost:1337
```

Of course, the `NEXT_PUBLIC_STRAPI_API_TOKEN` and `NEXT_PUBLIC_STRAPI_FORM_SUBMISSION_TOKEN` will be a little bit different.

## Step 6: Setup middleware

Create a `i18n-config.ts` file in the root of your `frontend` folder to set up the internationalization configuration.

```bash
touch frontend/i18n-config.ts
```

Add the following code to `i18n-config.ts`:

```ts
export const i18n = {
    defaultLocale: 'en',
    locales: ['en', 'de', 'cs'],
} as const;

export type Locale = typeof i18n['locales'][number];
```

In your `frontend/src/app` directory, create a new directory named `[lang]` for handling the locales we defined in `i18n-config.ts`.

```bash
mkdir frontend/src/app/[lang]
```

Before the data request from the Strapi backend is completed, middleware is required to handle certain tasks in a Next.js frontend app.

Create a new file named `middleware.ts` in the `frontend/src/app/[lang]` directory.

```bash
touch frontend/src/app/[lang]/middleware.ts
```

The `middleware.ts` file serves the purpose of adding internationalization (i18n) support to the application. It ensures that the URL contains a locale segment and redirects the user to the appropriate URL if a locale is missing. Additionally, it determines the best-matched locale based on the request headers using language negotiation and the available locales.

>*Find out more about Next.js middleware on the [Routing: Middleware | Next.js](https://nextjs.org/docs/pages/building-your-application/routing/middleware) page.*

Open up `middleware.ts` in your text editor, and add the following code:

```ts
import type { NextRequest } from 'next/server';
import { NextResponse } from 'next/server';

import { i18n } from '../i18n-config';

import { match as matchLocale } from '@formatjs/intl-localematcher';
import Negotiator from 'negotiator';

function getLocale(request: NextRequest): string | undefined {
    // Negotiator expects plain object so we need to transform headers
    const negotiatorHeaders: Record<string, string> = {};
    request.headers.forEach((value, key) => (negotiatorHeaders[key] = value));

    // Use negotiator and intl-localematcher to get best locale
    let languages = new Negotiator({ headers: negotiatorHeaders }).languages();
    // @ts-ignore locales are readonly
    const locales: string[] = i18n.locales;
    return matchLocale(languages, locales, i18n.defaultLocale);
}

export function middleware(request: NextRequest) {
    const pathname = request.nextUrl.pathname;

    // // `/_next/` and `/api/` are ignored by the watcher, but we need to ignore files in `public` manually.
    // // If you have one
    if (
        [
            '/manifest.json',
            '/favicon.ico',
            // Your other files in `public`
        ].includes(pathname)
    )
        return;

    // Check if there is any supported locale in the pathname
    const pathnameIsMissingLocale = i18n.locales.every(
        (locale) => !pathname.startsWith(`/${locale}/`) && pathname !== `/${locale}`
    );

    // Redirect if there is no locale
    if (pathnameIsMissingLocale) {
        const locale = getLocale(request);

        // e.g. incoming request is /products
        // The new URL is now /en-US/products
        return NextResponse.redirect(new URL(`/${locale}/${pathname}`, request.url));
    }
}

export const config = {
    // Matcher ignoring `/_next/` and `/api/`
    matcher: ['/((?!_next).*)'],
};
```

The `middleware.ts ` file imports `NextRequest` and `NextResponse` types to handle incoming requests and generate responses in Next.js. It imports the `i18n` object from the `i18n-config` module we created earlier to access the available locales. The `matchLocale` function is used for matching and determining the best locale based on the request headers and available locales.

## Step 7: Fetch data from Strapi

In this step we will create two files, `api-helpers.ts` and `fetch-api.tsx` to fetch data from the Strapi API backend.

In your `frontend/src/app/[lang]` directory, create a new directory and name it `utils`. Execute these commands in the root of your `frontend` folder.

```bash
cd frontend
mkdir src/app/[lang]/utils
```

In the `utils` folder, create `api-helpers.ts` and `fetch-api.tsx`.

```bash
touch src/app/utils/api-helpers.ts src/app/utils/fetch-api.tsx
```

Add the following code to `api-helpers.ts`:

```ts
// ./frontend/src/app/[lang]/utils/api-helpers.ts

export function getStrapiURL(path = '') {
    return `${process.env.NEXT_PUBLIC_STRAPI_API_URL || 'http://localhost:1337'}${path}`;
}

export function getStrapiMedia(url: string | null) {
    if (url == null) {
        return null;
    }

    // Return the full URL if the media is hosted on an external provider
    if (url.startsWith('http') || url.startsWith('//')) {
        return url;
    }

    // Otherwise prepend the URL path with the Strapi URL
    return `${getStrapiURL()}${url}`;
}

export function formatDate(dateString: string) {
    const date = new Date(dateString);
    const options: Intl.DateTimeFormatOptions = { year: 'numeric', month: 'long', day: 'numeric' };
    return date.toLocaleDateString('en-US', options);
}
```

`api-helpers.ts` defines two helper functions: `getStrapiURL` and `getStrapiMedia`. 

- `getStrapiURL`: This function returns the Strapi API URL. If the `NEXT_PUBLIC_STRAPI_API_URL` environment variable is set it will use the value as a base URL otherwise it defaults to `http://localhost:1337`. It takes an optional `path` parameter that represents a path to a specific endpoint. It concatenates the base URL with the provided `path` and returns the complete URL.

- `getStrapiMedia`: This function returns the correct URL for media regardless of whether the media is hosted externally or within the Strapi API.

These helper functions provide a convenient way to handle the Strapi API URL and media URLs for our frontend app.

Add the following code to `fetch-api.tsx`:

```tsx
// ./frontend/stc/app/[lang]/utils/fetch-api.tsx
import qs from "qs";
import { getStrapiURL } from "./api-helpers";

export async function fetchAPI(
  path: string,
  urlParamsObject = {},
  options = {}
) {
  try {
    // Merge default and user options
    const mergedOptions = {
      next: { revalidate: 60 },
      headers: {
        "Content-Type": "application/json",
      },
      ...options,
    };

    // Build request URL
    const queryString = qs.stringify(urlParamsObject);
    const requestUrl = `${getStrapiURL(
      `/api${path}${queryString ? `?${queryString}` : ""}`
    )}`;

    // Trigger API call
    const response = await fetch(requestUrl, mergedOptions);
    const data = await response.json();
    return data;
    
  } catch (error) {
    console.error(error);
    throw new Error(`Please check if your server is running and you set all the required tokens.`);
  }
}
```

The provided code defines a helper function named `fetchAPI` that performs an API call using the [JavaScript Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API).

The code imports the `qs` library which provides query string parsing and formatting functionality. It also imports the `getStrapiURL` helper function from the `api-helpers.ts` module we created previously.

The `fetchAPI` functions takes three parameters: `path`, `urlParamsObject`, and `options`. `path` is the API endpoint path, `urlParamsObject` is an object containing URL parameters, and `options` is an object containing additional options for the API call.

The `requestUrl` is constructed by combining the Strapi API URL obtained from the `getStrapiURL` function with the provided `path` and any query string parameters present in the `urlParamsObject`. The `qs.stringify` function converts the `urlParamsObject` into a query string.

The API call is triggered using the `fetch` function passing in the `requestUrl` and `mergedOptions`. What is returned is `data` in JSON format.

Add the following code to `next.config.js` in the `frontend` directory to access both local and remote media sources but still use the built-in [Next.js Image Optimization API](https://nextjs.org/docs/pages/building-your-application/optimizing/images)

```js
// ./frontend/next.config.js

/** @type {import('next').NextConfig} */

const nextConfig = {
  experimental: {
    appDir: true,
  },
  images: {
    remotePatterns: [
      {
        protocol: 'http',
        hostname: 'localhost',
        port: '1337',
        pathname: '/uploads/**',
      },
      {
        protocol: 'https',
        hostname: 'images.pexels.com',
      }
    ],
  },
}

module.exports = nextConfig
```

