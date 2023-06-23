# Build a blog with Next (React.js) and Strapi

Learn to create a blog website using Strapi for the backend and Next.js for the frontend.

## Introduction

[Strapi is the leading open-source composable Content Management System (CMS)](https://strapi.io). It is 100% JavaScript, based on Node.js, and used to build RESTful APIs and GraphQL APIs. It also has a cloud SAAS, ☁ [Strapi Cloud](https://strapi.io/cloud) ☁ - a fully managed, composable, and collaborative platform to boost your content velocity!

Strapi enables developers to build projects faster with a flexible and customizable platform for managing content. Check out the [Strapi Quickstart](https://docs.strapi.io/dev-docs/quick-start) guide for a brief intro.

[The Strapi Team made a New Strapi Starter](https://strapi.io/blog/introducing-the-new-strapi-starter-with-nextjs13-tailwind-and-typescript) using Next.js 13, Tailwind, and TypeScript. In this tutorial, you will learn to create a blog website based on the new Strapi Starter. The blog will use Strapi for the backend and Next.js 13 for the frontend.

## Before Getting Started

Check out the [project repo here](https://github.com/Marktawa/blog-strapi).

## Prerequisites

- This tutorial uses Strapi `v4.10.x`
- Node `v16.x.x` or `v18.x.x`. You can [download Node.js here.](https://nodejs.org/en/download)
- yarn `v1.22.x`. You can download [yarn here.](https://yarnpkg.com/package/download)
- Familiarity with Linux shell.

## Setup Project folder

Open up your terminal and create a `blog-strapi` folder to store your project files.

```bash
mkdir blog-strapi
```

Navigate into `blog-strapi`.

```bash
cd blog-strapi
```

## Create Standard Strapi App

Create your Strapi app in a folder named `backend`.

```bash
npx create-strapi-app@4.10.4 backend --quickstart
```

> **NOTE:**
>
> At the time of making this tutorial there was a bug in the file import in versions after 4.10.4, so please use this version for a smooth experience.

The `--quickstart` flag sets up your Strapi app with an [SQLite](https://www.sqlite.org/index.html) database and automatically starts your server on port `1337`.

> **TIP:**
>
> If the server is not already running, in your terminal, `cd` into the `backend` folder and run `yarn develop` to launch it.

Visit `http://localhost:1337/admin` in your browser and register your details in the Strapi Admin Registration Form.

![Strapi Admin Registration Form](https://i.imgur.com/OWSA5zP.png)

After registering your admin user, you should see the `Strapi Dashboard` in your browser.

![Strapi Admin Dashboard](https://i.imgur.com/ydaKhl9.png)

## Seeding The Data

Next, you will seed your Strapi app database with data to use for the blog post. This includes articles, authors, images, categories, and so on. The [Data import](https://docs.strapi.io/dev-docs/data-management/import) feature is quite handy in that regard.

Go back to your terminal and stop your Strapi backend server by pressing `CTRL` plus `C` on your keyboard. 

Update the schema for your Strapi app so that the data won't be lost when you perform a data import. Make sure you are executing the command in the root of your project folder `blog-strapi`.

> **NOTE:**
>
> You may need to install `wget` via `brew` for Mac users.

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

After a successful import, rerun your Strapi backend server. 

```bash
yarn develop
```

In your browser, login to your admin panel. You should see the newly imported `content` and `collection types`.

![Updated Dashboard](https://i.imgur.com/XguNXME.png)

## Create a Standard Next.js App

In a new terminal session, change the directory to `blog-strapi` and run the following command:

```bash
yarn create next-app
```

On installation, you'll see some prompts. Name your project `frontend` and refer to the image below for the other responses.

![Create Next App Prompts](https://i.imgur.com/KIXosho.png)

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

Delete the following auto-generated files in your `frontend` directory as we will not need them:
- `src/app/favicon.ico`
- `src/app/layout.tsx`
- `src/app/page.tsx`
- `src/app/globals.css`

## Set up Strapi API Token

Create a `.env` file in the root of your `frontend` directory and paste the following environment variables:

```
NEXT_PUBLIC_STRAPI_API_TOKEN=your-api-token
NEXT_PUBLIC_PAGE_LIMIT=6
NEXT_PUBLIC_STRAPI_API_URL=http://localhost:1337
```

Go back to your Strapi Admin panel to create the API token to be used for displaying content.

Inside your Strapi Admin Panel, click on `Settings`, select `API Tokens` and click on the `+ Create new API Token` button.

![Create new API Token](https://i.imgur.com/zBbwPQ3.png)

Configure your API Token with the following details:

- Name: `Public API Token Content`
- Description: `Access to public content`
- Token duration: `Unlimited`
- Token type: `Custom`

![Public API Token Content Details](https://i.imgur.com/2OMPlV5.png)

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

Your updated `.env` file should have environment variables similar to this:

```
NEXT_PUBLIC_STRAPI_API_TOKEN=ab2bfc3f905e61b58cbb76c1d459609ac29351249f5ffb98db9dd6d109ab60d651de15e0d31e3e367a754e622fe3c918fef89f0c28ae7b9c6e788d3db4d2ea7abd7b282b37b8448886c865607965ea82c57648e11de6bb278c7bbac811051287dbf6657cac0604716fd4d4501771db43b36b531b8418ca5c8a4bc248eb85b266
NEXT_PUBLIC_PAGE_LIMIT=6
NEXT_PUBLIC_STRAPI_API_URL=http://localhost:1337
```

Of course, the `NEXT_PUBLIC_STRAPI_API_TOKEN` for your Strapi server app will be different from the one shown above.

## Setup Middleware

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

Before the data request from the Strapi backend is completed, middleware is required to handle certain tasks in a Next.js frontend app.

Create a new file named `middleware.ts` in the `frontend/src` directory.

```bash
touch frontend/src/middleware.ts
```

The `middleware.ts` file serves the purpose of adding internationalization (i18n) support to the application. It ensures that the URL contains a locale segment and redirects the user to the appropriate URL if a locale is missing. Additionally, it determines the best-matched locale based on the request headers using language negotiation and the available locales.

>*Find out more about Next.js middleware on the [Routing: Middleware | Next.js](https://nextjs.org/docs/pages/building-your-application/routing/middleware) page.*

Open up `middleware.ts` and add the following code:

```ts
// ./frontend/src/middleware.ts

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

## Fetch Data From Strapi

In this step we will create two files, `api-helpers.ts` and `fetch-api.tsx` to fetch data from the Strapi API backend.

In your `frontend/src/app` directory, create a new directory named `[lang]` for handling the locales we defined in `i18n-config.ts`. This is handled through Next.js' [dynamic routes](https://nextjs.org/docs/pages/building-your-application/routing/dynamic-routes) feature.

```bash
mkdir frontend/src/app/[lang]
```

In your `frontend/src/app/[lang]` directory, create a new directory and name it `utils`. Execute these commands in the root of your `frontend` folder.

```bash
cd frontend
mkdir src/app/[lang]/utils
```

In the `utils` folder, create `api-helpers.ts` and `fetch-api.tsx`.

```bash
touch src/app/[lang]/utils/api-helpers.ts src/app/[lang]/utils/fetch-api.tsx
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

## Create Home Page for Blog

​​You will create a home page for your blog frontend in this step. In this case, the home page will display a list of all the articles.

### Add `page.tsx` file

Create a new file named `page.tsx` in the `src/app/[lang]` directory.

```bash
touch src/app/[lang]/page.tsx
```

Add the following code to `page.tsx`:

```tsx
"use client";
import { useState, useEffect, useCallback } from "react";
import { fetchAPI } from "./utils/fetch-api";

import Loader from "./components/Loader";
import PostList from "./components/PostList";
import PageHeader from "./components/PageHeader";

interface Meta {
  pagination: {
    start: number;
    limit: number;
    total: number;
  };
}

export default function Profile() {
  const [meta, setMeta] = useState<Meta | undefined>();
  const [data, setData] = useState<any>([]);
  const [isLoading, setLoading] = useState(true);

  const fetchData = useCallback(async (start: number, limit: number) => {
    setLoading(true);
    try {
      const token = process.env.NEXT_PUBLIC_STRAPI_API_TOKEN;
      const path = `/articles`;
      const urlParamsObject = {
        sort: { createdAt: "desc" },
        populate: {
          cover: { fields: ["url"] },
          category: { populate: "*" },
          authorsBio: {
            populate: "*",
          },
        },
        pagination: {
          start: start,
          limit: limit,
        },
      };
      const options = { headers: { Authorization: `Bearer ${token}` } };
      const responseData = await fetchAPI(path, urlParamsObject, options);

      if (start === 0) {
        setData(responseData.data);
      } else {
        setData((prevData: any[] ) => [...prevData, ...responseData.data]);
      }

      setMeta(responseData.meta);
    } catch (error) {
      console.error(error);
    } finally {
      setLoading(false);
    }
  }, []);

  function loadMorePosts(): void {
    const nextPosts = meta!.pagination.start + meta!.pagination.limit;
    fetchData(nextPosts, Number(process.env.NEXT_PUBLIC_PAGE_LIMIT));
  }

  useEffect(() => {
    fetchData(0, Number(process.env.NEXT_PUBLIC_PAGE_LIMIT));
  }, [fetchData]);

  if (isLoading) return <Loader />;

  return (
    <div>
      <PageHeader heading="Our Blog" text="Checkout Something Cool" />
      <PostList data={data}>
        {meta!.pagination.start + meta!.pagination.limit <
          meta!.pagination.total && (
          <div className="flex justify-center">
            <button
              type="button"
              className="px-6 py-3 text-sm rounded-lg hover:underline dark:bg-gray-900 dark:text-gray-400"
              onClick={loadMorePosts}
            >
              Load more posts...
            </button>
          </div>
        )}
      </PostList>
    </div>
  );
}
```

This code sets up a Next.js page that will fetch a list of all the articles from the Strapi API `/articles`endpoint and renders them in a neat blog-like format. It includes pagination for loading more posts when the `Load more posts` button is clicked.

The `fetchData` function uses the `fetchAPI` function to make the API request. It retrieves the `NEXT_PUBLIC_STRAPI_API_TOKEN` from the environment variables to construct the request path and URL parameters.

Notice that the `page.tsx` file imports a `Loader`, a `PostList`, and a `PageHeader` components, but these are yet to be created. Let's create the files.

Create a `components` folder in the `src/app/[lang]/` folder to store all your components:

```bash
mkdir src/app/[lang]/components
```

### Add Loader Component

Add a Loader component to provide visual feedback to the user that data is still being fetched from the API.

Create a new file named `Loader.tsx` in the `components` folder and add the following code to `Loader.tsx`

```tsx
// ./frontend/src/app/[lang]/components/Loader.tsx
export default function Loader() {
  return (
    <div className="absolute inset-0 flex items-center justify-center z-50  bg-opacity-40 bg-gray-500">
    <div role="status">
      <svg
        aria-hidden="true"
        className="inline w-8 h-8 mr-2 text-gray-200 animate-spin dark:text-gray-600 fill-purple-400"
        viewBox="0 0 100 101"
        fill="none"
        xmlns="http://www.w3.org/2000/svg"
      >
        <path
          d="M100 50.5908C100 78.2051 77.6142 100.591 50 100.591C22.3858 100.591 0 78.2051 0 50.5908C0 22.9766 22.3858 0.59082 50 0.59082C77.6142 0.59082 100 22.9766 100 50.5908ZM9.08144 50.5908C9.08144 73.1895 27.4013 91.5094 50 91.5094C72.5987 91.5094 90.9186 73.1895 90.9186 50.5908C90.9186 27.9921 72.5987 9.67226 50 9.67226C27.4013 9.67226 9.08144 27.9921 9.08144 50.5908Z"
          fill="currentColor"
        />
        <path
          d="M93.9676 39.0409C96.393 38.4038 97.8624 35.9116 97.0079 33.5539C95.2932 28.8227 92.871 24.3692 89.8167 20.348C85.8452 15.1192 80.8826 10.7238 75.2124 7.41289C69.5422 4.10194 63.2754 1.94025 56.7698 1.05124C51.7666 0.367541 46.6976 0.446843 41.7345 1.27873C39.2613 1.69328 37.813 4.19778 38.4501 6.62326C39.0873 9.04874 41.5694 10.4717 44.0505 10.1071C47.8511 9.54855 51.7191 9.52689 55.5402 10.0491C60.8642 10.7766 65.9928 12.5457 70.6331 15.2552C75.2735 17.9648 79.3347 21.5619 82.5849 25.841C84.9175 28.9121 86.7997 32.2913 88.1811 35.8758C89.083 38.2158 91.5421 39.6781 93.9676 39.0409Z"
          fill="currentFill"
        />
      </svg>
      <span className="sr-only">Loading...</span>
    </div>
  </div>
  )
}
```

### Add PostList Component

The `PostList` component will render a grid of articles and retrieve the necessary attributes like the `title`, `description`, `slug`, `cover`, and `author` for each article.

Create a new file named `PostList.tsx` in the `components` folder and add the following code to `PostList.tsx`

```tsx
// ./frontend/src/app/[lang]/components/PostList.tsx

import Image from "next/image";
import Link from "next/link";
import { getStrapiMedia, formatDate } from "../utils/api-helpers";

interface Article {
  id: 4;
  attributes: {
    title: string;
    description: string;
    slug: string;
    createdAt: string;
    updatedAt: string;
    publishedAt: string;
    cover: {
      data: {
        attributes: {
          url: string;
        };
      };
    };
    category: {
      data: {
        attributes: {
          name: string;
          slug: string;
        };
      };
    };
    authorsBio: {
      data: {
        attributes: {
          name: string;
          avatar: {
            data: {
              attributes: {
                url: string;
              };
            };
          };
        };
      };
    };
  };
}

export default function PostList({
  data: articles,
  children,
}: {
  data: Article[];
  children?: React.ReactNode;
}) {
  return (
    <section className="container p-6 mx-auto space-y-6 sm:space-y-12">
      <div className="grid justify-center grid-cols-1 gap-6 sm:grid-cols-2 lg:grid-cols-3">
        {articles.map((article) => {
          const imageUrl = getStrapiMedia(
            article.attributes.cover.data?.attributes.url
          );

          const category = article.attributes.category.data?.attributes;
          const authorsBio = article.attributes.authorsBio.data?.attributes;

          const avatarUrl = getStrapiMedia(
            authorsBio?.avatar.data.attributes.url
          );

          return (
            <Link
              href={`${category?.slug}/${article.attributes.slug}`}
              key={article.id}
              className="max-w-sm mx-auto group hover:no-underline focus:no-underline dark:bg-gray-900 lg:w-[300px] xl:min-w-[375px] rounded-2xl overflow-hidden shadow-lg"
            >
              {imageUrl && (
                <Image
                  alt="presentation"
                  width="240"
                  height="240"
                  className="object-cover w-full h-44 "
                  src={imageUrl}
                />
              )}
              <div className="p-6 space-y-2 relative">
                {avatarUrl && (
                  <Image
                    alt="avatar"
                    width="80"
                    height="80"
                    src={avatarUrl}
                    className="rounded-full h-16 w-16 object-cover absolute -top-8 right-4"
                  />
                )}

                <h3 className="text-2xl font-semibold group-hover:underline group-focus:underline">
                  {article.attributes.title}
                </h3>

                <div className="flex justify-between items-center">
                  <span className="text-xs dark:text-gray-400">
                    {formatDate(article.attributes.publishedAt)}
                  </span>
                  {authorsBio && (
                    <span className="text-xs dark:text-gray-400">
                      {authorsBio.name}
                    </span>
                  )}
                </div>
                <p className="py-4">{article.attributes.description}</p>
              </div>
            </Link>
          );
        })}
      </div>
      {children && children}
    </section>
  );
}
```

The `PostList` component provides a reusable way to display a list of blog posts with consistent styling and structure.

### Add PageHeader Component

The `PageHeader` component will add a reusable header to your frontend.

Create a new file named `PageHeader.tsx` in the `components` folder and add the following code to `PageHeader.tsx`.

```tsx
// ./frontend/src/app/[lang]/components/PageHeader.tsx

import React from "react";

interface PageHeaderProps {
  heading: string,
  text?: string,
}

export default function PageHeader({ heading, text } : PageHeaderProps) {
  return (
    <div className="my-16 w-full text-center">
    { text && <span className="text-violet-400 font-bold">{text}</span> }
    <h2 className="text-4xl my-4 lg:text-5xl font-bold font-heading">{heading}</h2>
  </div>
  );
}
```

### Create Layout

Let's create a layout that provides code for the UI. This will be shared between routes.

Create a new file named `layout.tsx` in the `src/app/[lang]` directory.

```bash
touch src/app/[lang]/layout.tsx
```

Add the following code to `layout.tsx`:

```tsx
// ./frontend/src/app/[lang]/layout.tsx

import type { Metadata } from "next";
import "./globals.css";
import { getStrapiMedia, getStrapiURL } from "./utils/api-helpers";
import { fetchAPI } from "./utils/fetch-api";

import { i18n } from "../../../i18n-config";
import Footer from "./components/Footer";
import Navbar from "./components/Navbar";

const FALLBACK_SEO = {
  title: "Strapi Starter Next Blog",
  description: "Strapi Starter Next Blog",
}


async function getGlobal(): Promise<any> {
  const token = process.env.NEXT_PUBLIC_STRAPI_API_TOKEN;

  if (!token) throw new Error("The Strapi API Token environment variable is not set.");

  const path = `/global`;
  const options = { headers: { Authorization: `Bearer ${token}` } };

  const urlParamsObject = {
    populate: [
      "metadata.shareImage",
      "favicon",
      "navbar.links",
      "navbar.navbarLogo.logoImg",
      "footer.footerLogo.logoImg",
      "footer.menuLinks",
      "footer.legalLinks",
      "footer.socialLinks",
      "footer.categories",
    ],
  };

  const response = await fetchAPI(path, urlParamsObject, options);
  return response;
}

export async function generateMetadata(): Promise<Metadata> {
  const meta = await getGlobal();

  if (!meta.data) return FALLBACK_SEO;

  const { metadata, favicon } = meta.data.attributes;
  const { url } = favicon.data.attributes;

  return {
    title: metadata.metaTitle,
    description: metadata.metaDescription,
    icons: {
      icon: [new URL(url, getStrapiURL())],
    },
  };
}

export default async function RootLayout({
  children,
  params,
}: {
  children: React.ReactNode;
  params: { lang: string };
}) {
  const global = await getGlobal();
  // TODO: CREATE A CUSTOM ERROR PAGE
  if (!global.data) return null;
  
  const { navbar, footer } = global.data.attributes;

  const navbarLogoUrl = getStrapiMedia(
    navbar.navbarLogo.logoImg.data.attributes.url
  );

  const footerLogoUrl = getStrapiMedia(
    footer.footerLogo.logoImg.data.attributes.url
  );

  return (
    <html lang={params.lang}>
      <body>
        <Navbar
          links={navbar.links}
          logoUrl={navbarLogoUrl}
          logoText={navbar.navbarLogo.logoText}
        />

        <main className="dark:bg-black dark:text-gray-100 min-h-screen">
          {children}
        </main>

        <Footer
          logoUrl={footerLogoUrl}
          logoText={footer.footerLogo.logoText}
          menuLinks={footer.menuLinks}
          categoryLinks={footer.categories.data}
          legalLinks={footer.legalLinks}
          socialLinks={footer.socialLinks}
        />
      </body>
    </html>
  );
}

export async function generateStaticParams() {
  return i18n.locales.map((locale) => ({ lang: locale }));
}
```

The `layout.tsx` provides a consistent layout structure for the blog frontend. It fetches global data from the Strapi API, retrieves metadata, and renders the necessary components such as the `Navbar` and the `Footer`. It also handles internationalization by generating static parameters based on the available locales.

Notice that the code imports a `globals.css` file, a `Footer` component, and a `Navbar` component but they are yet to be created. In addition, a `Logo` component for the blog logo will be needed. This will be used by the `Navbar` and `Footer` components. Let's create the files.

### Add Logo component

Create a new file named `Logo.tsx` in the `components` folder and add the following code to `Logo.tsx`:

```tsx
// ./frontend/src/app/[lang]/components/Logo.tsx

import React from "react";
import Link from "next/link";
import Image from "next/image";

export default function Logo({
  src,
  children,
}: {
  src: string | null;
  children?: React.ReactNode;
}) {
  return (
    <Link
      href="/"
      aria-label="Back to homepage"
      className="flex items-center p-2"
    >
      {src && <Image src={src} alt="logo" width={45} height={45} />}
      <div className="ml-2">{children}</div>
    </Link>
  );
}
```

### Add Navbar component

Create a new file named `Navbar.tsx` in the `components` folder and add the following code to `Navbar.tsx`:

```tsx
// ./frontend/src/app/[lang]/components/Navbar.tsx

"use client";
import Logo from "./Logo";
import Link from "next/link";
import { usePathname } from "next/navigation";

interface NavLink {
  id: number;
  url: string;
  newTab: boolean;
  text: string;
}

function NavLink({ url, text }: NavLink) {
  const path = usePathname();

  return (
    <li className="flex">
      <Link
        href={url}
        className={`flex items-center mx-4 -mb-1 border-b-2 dark:border-transparent ${
          path === url && "dark:text-violet-400 dark:border-violet-400"
        }}`}
      >
        {text}
      </Link>
    </li>
  );
}

export default function Navbar({
  links,
  logoUrl,
  logoText,
}: {
  links: Array<NavLink>;
  logoUrl: string | null;
  logoText: string | null;
}) {
  return (
    <div className="p-4 dark:bg-black dark:text-gray-100">
      <div className="container flex justify-between h-16 mx-auto px-0 sm:px-6">
        <Logo src={logoUrl}>
          {logoText && <h2 className="text-2xl font-bold">{logoText}</h2>}
        </Logo>

        <div className="items-center flex-shrink-0 hidden lg:flex">
          <ul className="items-stretch hidden space-x-3 lg:flex">
            {links.map((item: NavLink) => (
              <NavLink key={item.id} {...item} />
            ))}
          </ul>
        </div>

        <button className="p-4 lg:hidden">
          <svg
            xmlns="http://www.w3.org/2000/svg"
            fill="none"
            viewBox="0 0 24 24"
            stroke="currentColor"
            className="w-6 h-6 dark:text-gray-100"
          >
            <path
              strokeLinecap="round"
              strokeLinejoin="round"
              strokeWidth="2"
              d="M4 6h16M4 12h16M4 18h16"
            ></path>
          </svg>
        </button>
      </div>
    </div>
  );
}
```

### Add Footer component

Create a new file named `Footer.tsx` in the `components` folder and add the following code to `Footer.tsx`:

```tsx
"use client";
import { usePathname } from "next/navigation";
import Link from "next/link";
import Logo from "./Logo";
import { CgWebsite } from "react-icons/cg";
import { FaDiscord } from "react-icons/fa";
import { AiFillTwitterCircle, AiFillYoutube } from "react-icons/ai";

interface FooterLink {
  id: number;
  url: string;
  newTab: boolean;
  text: string;
  social?: string;
}

interface CategoryLink {
  id: string;
  attributes: {
    name: string;
    slug: string;
  };
}

function FooterLink({ url, text }: FooterLink) {
  const path = usePathname();
  return (
    <li className="flex">
      <Link
        href={url}
        className={`hover:dark:text-violet-400 ${
          path === url && "dark:text-violet-400 dark:border-violet-400"
        }}`}
      >
        {text}
      </Link>
    </li>
  );
}

function CategoryLink({ attributes }: CategoryLink) {
  return (
    <li className="flex">
      <Link
        href={`/${attributes.slug}`}
        className="hover:dark:text-violet-400"
      >
        {attributes.name}
      </Link>
    </li>
  );
}

function RenderSocialIcon({ social }: { social: string | undefined }) {
  switch (social) {
    case "WEBSITE":
      return <CgWebsite />;
    case "TWITTER":
      return <AiFillTwitterCircle />;
    case "YOUTUBE":
      return <AiFillYoutube />;
    case "DISCORD":
      return <FaDiscord />;
    default:
      return null;
  }
}

export default function Footer({
  logoUrl,
  logoText,
  menuLinks,
  categoryLinks,
  legalLinks,
  socialLinks,
}: {
  logoUrl: string | null;
  logoText: string | null;
  menuLinks: Array<FooterLink>;
  categoryLinks: Array<CategoryLink>;
  legalLinks: Array<FooterLink>;
  socialLinks: Array<FooterLink>;
}) {

  return (
    <footer className="py-6 dark:bg-black dark:text-gray-50">
      <div className="container px-6 mx-auto space-y-6 divide-y divide-gray-400 md:space-y-12 divide-opacity-50">
        <div className="grid grid-cols-12">
          <div className="pb-6 col-span-full md:pb-0 md:col-span-6">
            <Logo src={logoUrl}>
              {logoText && <h2 className="text-2xl font-bold">{logoText}</h2>}
            </Logo>
          </div>

          <div className="col-span-6 text-center md:text-left md:col-span-3">
            <p className="pb-1 text-lg font-medium">Categories</p>
            <ul>
              {categoryLinks.map((link: CategoryLink) => (
                <CategoryLink key={link.id} {...link} />
              ))}
            </ul>
          </div>

          <div className="col-span-6 text-center md:text-left md:col-span-3">
            <p className="pb-1 text-lg font-medium">Menu</p>
            <ul>
              {menuLinks.map((link: FooterLink) => (
                <FooterLink key={link.id} {...link} />
              ))}
            </ul>
          </div>
        </div>
        <div className="grid justify-center pt-6 lg:justify-between">
          <div className="flex">
            <span className="mr-2">
              ©{new Date().getFullYear()} All rights reserved
            </span>
            <ul className="flex">
              {legalLinks.map((link: FooterLink) => (
                <Link
                  href={link.url}
                  className="text-gray-400 hover:text-gray-300 mr-2"
                  key={link.id}
                >
                  {link.text}
                </Link>
              ))}
            </ul>
          </div>
          <div className="flex justify-center pt-4 space-x-4 lg:pt-0 lg:col-end-13">
            {socialLinks.map((link: FooterLink) => {
              return (
                <a
                  key={link.id}
                  rel="noopener noreferrer"
                  href={link.url}
                  title={link.text}
                  target={link.newTab ? "_blank" : "_self"}
                  className="flex items-center justify-center w-10 h-10 rounded-full dark:bg-violet-400 dark:text-gray-900"
                >
                  <RenderSocialIcon social={link.social} />
                </a>
              );
            })}
          </div>
        </div>
      </div>
    </footer>
  );
}
```

### Add `globals.css`

Create a new file called `globals.css` in the `[lang]` folder and add the following code to `globals.css`:

```css
/* ./frontend/src/app/[lang]/globals.css */

@tailwind base;
@tailwind components;
@tailwind utilities;
@import "react-slideshow-image/dist/styles.css";

body {
  @apply dark:bg-black
}


/******************************************* 
  Rich Text Styles
*******************************************/ 

/* Headers */
.rich-text h1 {
  @apply text-4xl font-bold mb-4 text-violet-400;
}

.rich-text h2 {
  @apply text-3xl font-bold mb-4 text-violet-400;
}

.rich-text h3 {
  @apply text-2xl font-bold mb-4 text-violet-400;
}

.rich-text h4 {
  @apply text-xl font-bold mb-4 text-violet-400;
}

.rich-text h5 {
  @apply text-lg font-bold mb-4 text-violet-400;
}

.rich-text h6 {
  @apply text-base font-bold mb-4 text-violet-200;
}

/* Horizontal rules */
.rich-text hr {
  @apply border-gray-300 my-8;
}

.rich-text a {
  @apply text-violet-400 underline;
}

/* Typographic replacements */
.rich-text p {
  @apply mb-4;
}

/* Emphasis */
.rich-text strong {
  @apply font-bold;
}

.rich-text em {
  @apply italic;
}

.rich-text del {
  @apply line-through;
}

/* Blockquotes */
.rich-text blockquote {
  @apply border-l-4 border-gray-400 pl-4 py-2 mb-4;
}

/* Lists */
.rich-text ul {
  @apply list-disc pl-4 mb-4;
}

.rich-text ol {
  @apply list-decimal pl-4 mb-4;
}

.rich-text li {
  @apply mb-2;
}

.rich-text li > ul {
  @apply list-disc pl-4 mb-2;
}

.rich-text li > ol {
  @apply list-decimal pl-4 mb-2;
}

/* Code blocks */
.rich-text pre {
  @apply font-mono bg-gray-900 text-gray-100 rounded p-4  my-6;
}

.rich-text code {
  @apply font-mono bg-gray-900 text-gray-100 rounded px-2 py-1;
}

/* Tables */
.rich-text table {
  @apply w-full border-collapse border-gray-300 my-6;
}

.rich-text th {
  @apply bg-gray-900 text-left py-2 px-4 font-semibold border-b border-gray-300;
}

.rich-text td {
  @apply py-2 px-4 border-b border-gray-300;
}

/* Images */
.rich-text img {
  @apply w-full object-cover rounded-xl my-6;
}

/* Custom containers */
.rich-text .warning {
  @apply bg-yellow-100 border-yellow-500 text-yellow-700 px-4 py-2 rounded-lg mb-4;
}


/******************************************* 
  React Slideshow Styles
*******************************************/   

.react-slideshow-container [aria-label="Previous Slide"] {
  margin-left: 1rem;
}

.react-slideshow-container [aria-label="Next Slide"] {
  margin-right: 1rem;
}
```

### Test Home Page

Make sure your Strapi backend server is running, then start your Next.js frontend development server.

Run the following command in the `frontend` directory:

```bash
yarn dev
```

Visit `http://localhost:3000` in your browser and you should your blog’s home page..

![Blog Home Page](https://i.imgur.com/yKIMWiC.png)

Great! The home page is working and is displaying a grid of all posts as expected. 

## Create Category Page

The next step is to create a page for each category.

### Create `[category]` folder

Create a folder named `[category]` inside the `[lang]` folder. Since we have multiple categories we will use [Dynamic Segments](https://nextjs.org/docs/pages/building-your-application/routing/dynamic-routes#convention) for handling the routes for each category. Therefore the URL for each category will be based on its name.

```bash
mkdir src/app/[lang]/[category]
```

### Add `page.tsx` file

Create a file called `page.tsx` in the `[category]` folder.

```bash
touch src/app/[lang]/[category]/page.tsx
```

Add the following code to `page.tsx`:

```tsx
import PageHeader from '@/app/[lang]/components/PageHeader';
import { fetchAPI } from '@/app/[lang]/utils/fetch-api';
import PostList from '@/app/[lang]/components/PostList';

async function fetchPostsByCategory(filter: string) {
    try {
        const token = process.env.NEXT_PUBLIC_STRAPI_API_TOKEN;
        const path = `/articles`;
        const urlParamsObject = {
            sort: { createdAt: 'desc' },
            filters: {
                category: {
                    slug: filter,
                },
            },
            populate: {
                cover: { fields: ['url'] },
                category: {
                    populate: '*',
                },
                authorsBio: {
                    populate: '*',
                },
            },
        };
        const options = { headers: { Authorization: `Bearer ${token}` } };
        const responseData = await fetchAPI(path, urlParamsObject, options);
        return responseData;
    } catch (error) {
        console.error(error);
    }
}

export default async function CategoryRoute({ params }: { params: { category: string } }) {
    const filter = params.category;
    const { data } = await fetchPostsByCategory(filter);

    if (data.length === 0) return <div>Not Posts In this category</div>;

    const { name, description } = data[0]?.attributes.category.data.attributes;

    return (
        <div>
            <PageHeader heading={name} text={description} />
            <PostList data={data} />
        </div>
    );
}

export async function generateStaticParams() {
    return [];
}
```

The provided code fetches and renders posts for a specific category. It exports two asynchronous functions: `CategoryRoute` and `generateStaticParams`.

The `CategoryRoute` function handles rendering content for a specific category. It retrieves the category from the provided parameters, calls the `fetchPostsByCategory` function to fetch posts data for that category, and then renders a page header with the category name and description, followed by a list of posts.

The `fetchPostsByCategory` function fetches posts by a given category. It constructs an API request with sorting, filtering, and population options, and retrieves the response data.

The `generateStaticParams` function is used for generating static paths based on dynamic routes. In this case, it returns an empty array.

Overall, the code retrieves and displays posts data for a specific category, utilizing asynchronous functions for API requests and rendering.

### Test Category Page

Make sure your Strapi backend server is running, then start your Next.js frontend development server.

Run the following command in the `frontend` directory:

```bash
yarn dev
```

Visit `http://localhost:3000/strapi` in your browser and you should see the `strapi` category page of your blog.

![Strapi Category Page](https://i.imgur.com/Ry1rNwx.png)

You can test the other categories by clicking on the `category` links in the footer of your blog.

![Category Links](https://i.imgur.com/dMTzf5E.png)

The category page should work and display a grid of posts related to the specific category.

## Create Article Page

The next step is to create a page for each article that displays the full post.

### Create `[slug]` folder

Create a folder called `[slug]` in the `[category]` folder. This implies that each article will be have a route based on its `slug` as defined in the Strapi Admin Panel. The `slug` is generated from the post `title`.

```bash
mkdir src/app/[lang]/[category]/[slug]
```

Inside the `[slug]` folder, create two new files:
- `page.tsx` for fetching and rendering the article.
- `layout.tsx` for the layout of the Article page.

```bash
touch src/app/[lang]/[category]/[slug]/page.tsx src/app/[lang]/[category]/[slug]/layout.tsx
```

### Add `page.tsx` file

Add the following code to `src/app/[lang]/[category]/[slug]/page.tsx`:

```tsx
import { fetchAPI } from '@/app/[lang]/utils/fetch-api';
import Post from '@/app/[lang]/components/Post';
import type { Metadata } from 'next';

async function getPostBySlug(slug: string) {
    const token = process.env.NEXT_PUBLIC_STRAPI_API_TOKEN;
    const path = `/articles`;
    const urlParamsObject = {
        filters: { slug },
        populate: {
            cover: { fields: ['url'] },
            authorsBio: { populate: '*' },
            category: { fields: ['name'] },
            blocks: { populate: '*' },
        },
    };
    const options = { headers: { Authorization: `Bearer ${token}` } };
    const response = await fetchAPI(path, urlParamsObject, options);
    return response;
}

async function getMetaData(slug: string) {
    const token = process.env.NEXT_PUBLIC_STRAPI_API_TOKEN;
    const path = `/articles`;
    const urlParamsObject = {
        filters: { slug },
        populate: { seo: { populate: '*' } },
    };
    const options = { headers: { Authorization: `Bearer ${token}` } };
    const response = await fetchAPI(path, urlParamsObject, options);
    return response.data;
}

export async function generateMetadata({ params }: { params: { slug: string } }): Promise<Metadata> {
    const meta = await getMetaData(params.slug);
    const metadata = meta[0].attributes.seo;

    return {
        title: metadata.metaTitle,
        description: metadata.metaDescription,
    };
}

export default async function PostRoute({ params }: { params: { slug: string } }) {
    const { slug } = params;
    const data = await getPostBySlug(slug);
    if (data.data.length === 0) return <h2>no post found</h2>;
    return <Post data={data.data[0]} />;
}

export async function generateStaticParams() {
    const token = process.env.NEXT_PUBLIC_STRAPI_API_TOKEN;
    const path = `/articles`;
    const options = { headers: { Authorization: `Bearer ${token}` } };
    const articleResponse = await fetchAPI(
        path,
        {
            populate: ['category'],
        },
        options
    );

    return articleResponse.data.map(
        (article: {
            attributes: {
                slug: string;
                category: {
                    slug: string;
                };
            };
        }) => ({ slug: article.attributes.slug, category: article.attributes.slug })
    );
}
```

The `page.tsx` file for individual posts displays an article by fetching post data using the `fetchAPI` helper function and uses the `Post` component to render the page.

`page.tsx` includes the following functions:

- `getPostBySlug`: Fetches post data based on a given slug, including `cover`, `authorsBio`, and `category`.
- `getMetaData`: Fetches metadata for a post based on a given slug.
- `generateMetadata`: Generates metadata for a post using the `getMetadata` function and returns the `title` and `description` of a post.
- `PostRoute`: Renders the post page based on a given `slug`. The function retrieves post data using the `getPostBySlug` function and renders the post using the `Post` component.
- `generateStaticParams`: Generates static parameters for articles by fetching the articles' data and mapping it to an array with the `slug` and `category` attributes as strings.

### Add `Post` component

`page.tsx` imports a `Post` component for rendering posts. Create a `Post.tsx` file in the `components` folder.

```bash
touch src/app/[lang]/components/Post.tsx
```

Add the following code to `Post.tsx`:

```tsx
import { formatDate, getStrapiMedia } from '@/app/[lang]/utils/api-helpers';
import { postRenderer } from '@/app/[lang]/utils/post-renderer';
import Image from 'next/image';

interface Article {
    id: number;
    attributes: {
        title: string;
        description: string;
        slug: string;
        cover: {
            data: {
                attributes: {
                    url: string;
                };
            };
        };
        authorsBio: {
            data: {
                attributes: {
                    name: string;
                    avatar: {
                        data: {
                            attributes: {
                                url: string;
                            };
                        };
                    };
                };
            };
        };
        blocks: any[];
        publishedAt: string;
    };
}

export default function Post({ data }: { data: Article }) {
    const { title, description, publishedAt, cover, authorsBio } = data.attributes;
    const author = authorsBio.data?.attributes;
    const imageUrl = getStrapiMedia(cover.data?.attributes.url);
    const authorImgUrl = getStrapiMedia(authorsBio.data?.attributes.avatar.data.attributes.url);

    return (
        <article className="space-y-8 dark:bg-black dark:text-gray-50">
            {imageUrl && (
                <Image
                    src={imageUrl}
                    alt="article cover image"
                    width={400}
                    height={400}
                    className="w-full h-96 object-cover rounded-lg"
                />
            )}
            <div className="space-y-6">
                <h1 className="leading-tight text-5xl font-bold ">{title}</h1>
                <div className="flex flex-col items-start justify-between w-full md:flex-row md:items-center dark:text-gray-400">
                    <div className="flex items-center md:space-x-2">
                        {authorImgUrl && (
                            <Image
                                src={authorImgUrl}
                                alt="article cover image"
                                width={400}
                                height={400}
                                className="w-14 h-14 border rounded-full dark:bg-gray-500 dark:border-gray-700"
                            />
                        )}
                        <p className="text-md dark:text-violet-400">
                            {author && author.name} • {formatDate(publishedAt)}
                        </p>
                    </div>
                </div>
            </div>

            <div className="dark:text-gray-100">
                <p>{description}</p>

                {data.attributes.blocks.map((section: any, index: number) => postRenderer(section, index))}
            </div>
        </article>
    );
}
```

The `Post` component renders details of an article. It expects a `data` prop with specific attributes which include a `title`, `description`, `slug`, `cover`, `authorsBio`, `blocks`, and `publishedAt`. It renders an article based on these attributes.

It maps over the `data.attributes.blocks` array and uses a `postFenderer` utility function to render each article section. 

### Create `postRenderer` utility

Let's create the `postRenderer` utility function to be used by the `Post` component.

Create a new file named `post-renderer.tsx` inside the `utils` folder.

```bash
touch src/app/[lang]/utils/post-renderer.tsx
```

Add the following code to `post-renderer.tsx`:

```tsx
import RichText from "../components/RichText";
import ImageSlider from "../components/ImageSlider";
import Quote from "../components/Quote";
import Media from "../components/Media";
import VideoEmbed from "../components/VideoEmbed";

export function postRenderer(section: any, index: number) {
  switch (section.__component) {
    case "shared.rich-text":
      return <RichText key={index} data={section} />;
    case "shared.slider":
      return <ImageSlider key={index} data={section} />;
    case "shared.quote": 
      return <Quote key={index} data={section} />;
    case "shared.media":
      return <Media key={index} data={section} />;
    case "shared.video-embed":
      return <VideoEmbed key={index} data={section} />;
    default:
      return null;
  }
}
```

The `postRenderer` utility function is used to dynamically render different types of sections within a post based on the `__component` value of each section. This could be a `RichText`, `ImageSlider`, `Quote`, `Media`, or `VideoEmbed` component

Next, we will create the respective components required by the `postRenderer` utility.

Create the `RichText`, `ImageSlider`, `Quote`, `Media`, and `VideoEmbed` components:

```bash
touch src/app/[lang]/components/{RichText,ImageSlider,Quote,Media,VideoEmbed}.tsx
```

### Add `RichText` Component

Add the following code to `RichText.tsx`:

```tsx
import Markdown from "react-markdown";
import remarkGfm from "remark-gfm";

interface RichTextProps {
  data: {
    body: string;
  };
}

export default function RichText({ data }: RichTextProps) {
  return (
    <section className="rich-text py-6 dark:bg-black dark:text-gray-50 ">
      <Markdown children={data.body} remarkPlugins={[remarkGfm]} />
    </section>
  );
}
```

The `RichText` component converts markdown-formatted text into HTML and renders it as a section of rich text content. It relies on the `Markdown` component from the `react-markdown` library and the `remark-gfm` plugin for rendering Markdown syntax with additional features like tables.

### Add `ImageSlider` component

Add the following code to `ImageSlider.tsx`:

```tsx
"use client";
import { Fade } from "react-slideshow-image";
import { getStrapiMedia } from "../utils/api-helpers";
import Image from "next/image";

interface Image {
  id: number;
  attributes: {
    alternativeText: string | null;
    caption: string | null;
    url: string;
  };
}

interface SlidShowProps {
  files: {
    data: Image[];
  };
}

export default function Slideshow({ data }: { data: SlidShowProps }) {
  return (
    <div className="slide-container">
      <Fade>
        {data.files.data.map((fadeImage: Image, index) => {
          const imageUrl = getStrapiMedia(fadeImage.attributes.url);
          return (
            <div key={index}>
              {imageUrl && <Image className="w-full h-96 object-cover rounded-lg" height={400} width={600} alt="alt text" src={imageUrl} />}
            </div>
          );
        })}
      </Fade>
    </div>
  );
}
```

The `Slideshow` component creates an image slider by rendering a series of images based on the provided data. The `Fade` component from the `react-slideshow-image` library provides the sliding effect, and the `Image` component from Next.js handles the rendering of each image.

### Add `Quote` component

Add the following code to `Quote.tsx`:

```tsx
interface QuoteProps {
    data: {
      title: string;
      body: string;
      author: string;
    };
  }
  
  export default function Quote({ data }: QuoteProps) {
    const { title, body, author } = data;
  
    return (
      <div className="flex flex-col items-center mx-12 lg:mx-0 py-44">
        {title && <h2 className="my-4">{title}</h2>}
        <div className="relative text-center">
          <svg
            xmlns="http://www.w3.org/2000/svg"
            viewBox="0 0 512 512"
            fill="currentColor"
            className="absolute top-0 -left-4 w-4 h-4 dark:text-gray-700"
          >
            <path d="M232,246.857V16H16V416H54.4ZM48,48H200V233.143L48,377.905Z"></path>
            <path d="M280,416h38.4L496,246.857V16H280ZM312,48H464V233.143L312,377.905Z"></path>
          </svg>
          <p className="px-6 py-1 text-lg italic">{body}</p>
          <svg
            xmlns="http://www.w3.org/2000/svg"
            viewBox="0 0 512 512"
            fill="currentColor"
            className="absolute bottom-0 -right-4 w-4 h-4 dark:text-gray-700"
          >
            <path d="M280,185.143V416H496V16H457.6ZM464,384H312V198.857L464,54.1Z"></path>
            <path d="M232,16H193.6L16,185.143V416H232ZM200,384H48V198.857L200,54.1Z"></path>
          </svg>
        </div>
        <span className="w-12 h-1 my-2 rounded-lg dark:bg-violet-400"></span>
        {author ? <p>{author}</p> : "unknown"}
      </div>
    );
  }
```

The `Quote` component renders a blockquote with a `title`, `body`, and an optional `author`. The quote symbols are displayed using SVG icons, and appropriate styling classes are applied for visual presentation.

### Add `Media` component

Add the following code to `Media.tsx`:

```tsx
import { getStrapiMedia } from "../utils/api-helpers";
import Image from "next/image";

interface MediaProps {
  file: {
    data: {
      id: string;
      attributes: {
        url: string;
        name: string;
        alternativeText: string;
      };
    };
  };
}

export default function Media({ data }: { data: MediaProps }) {
  const imgUrl = getStrapiMedia(data.file.data.attributes.url);
  return (
    <div className="flex items-center justify-center mt-8 lg:mt-0 h-72 sm:h-80 lg:h-96 xl:h-112 2xl:h-128">
      <Image
        src={imgUrl || ""}
        alt={data.file.data.attributes.alternativeText || "none provided"}
        className="object-cover w-full h-full rounded-lg overflow-hidden"
        width={400}
        height={400}
      />
    </div>
  );
}
```

The `Media` component renders an image or media file using the Next.js `Image` component. The image is displayed with rounded corners and is set to a specific width and height. The `getStrapiMedia` function is used to retrieve the media URL.

### Add `VideoEmbed` component

Add the following code to `VideoEmbed.tsx`:

```tsx
import React from "react";

interface VideoEmbedProps {
  id: number;
  url: string;
  width?: string;
  height?: string;
}

const getEmbedUrl = (videoUrl: string): string | null => {
  const youtubeRegex =
    /^.*(youtu.be\/|v\/|u\/\w\/|embed\/|watch\?v=|watch\?v%3D)([\w-]{11}).*/;
  const youtubeMatch = videoUrl.match(youtubeRegex);

  if (youtubeMatch && youtubeMatch[2].length === 11) {
    return `https://www.youtube.com/embed/${youtubeMatch[2]}`;
  }

  // Add support for other video platforms here

  return null;
};

export default function VideoEmbed({ data }: { data: VideoEmbedProps }) {
  const embedUrl = getEmbedUrl(data.url);

  if (!embedUrl) return <div>Invalid video URL</div>;

  return (
    <div className="video-embed relative pb-56.25 h-72 lg:h-[450px] overflow-hidden my-8">
      <iframe
        title="video"
        src={embedUrl || ""}
        width={data.width || "100%"}
        height={data.height || "100%"}
        allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture"
        allowFullScreen
        className="absolute top-0 left-0 w-full h-full"
      />
    </div>
  );
}
```

The `VideoEmbed` component renders an embedded video based on the provided URL. It supports YouTube videos by extracting the video ID and creating an embed URL. If the URL is invalid, it displays an error message. The embedded video is displayed within a container with specified dimensions, and the iframe allows for various video playback features.

### Add Layout

Add the following code to `src/app/[lang]/[category]/[slug]/layout.tsx`:

```tsx
import React from "react";

import ArticleSelect from "@/app/[lang]/components/ArticleSelect";
import { fetchAPI } from "@/app/[lang]/utils/fetch-api";

async function fetchSideMenuData(filter: string) {
  try {
    const token = process.env.NEXT_PUBLIC_STRAPI_API_TOKEN;
    const options = { headers: { Authorization: `Bearer ${token}` } };

    const categoriesResponse = await fetchAPI(
      "/categories",
      { populate: "*" },
      options
    );

    const articlesResponse = await fetchAPI(
      "/articles",
      filter
        ? {
            filters: {
              category: {
                name: filter,
              },
            },
          }
        : {},
      options
    );

    return {
      articles: articlesResponse.data,
      categories: categoriesResponse.data,
    };
  } catch (error) {
    console.error(error);
  }
}

interface Category {
  id: number;
  attributes: {
    name: string;
    slug: string;
    articles: {
      data: Array<{}>;
    };
  };
}

interface Article {
  id: number;
  attributes: {
    title: string;
    slug: string;
  };
}

interface Data {
  articles: Article[];
  categories: Category[];
}

export default async function LayoutRoute({
  params,
  children,
}: {
  children: React.ReactNode;
  params: {
    slug: string;
    category: string;
  };
}) {
  const { category } = params;
  const { categories, articles } = (await fetchSideMenuData(category)) as Data;

  return (
    <section className="container p-8 mx-auto space-y-6 sm:space-y-12">
      <div className="grid grid-cols-1 md:grid-cols-3 gap-2 lg:gap-4">
        <div className="col-span-2">{children}</div>
        <aside>
          <ArticleSelect
            categories={categories}
            articles={articles}
            params={params}
          />
        </aside>
      </div>
    </section>
  );
}

export async function generateStaticParams() {
  const token = process.env.NEXT_PUBLIC_STRAPI_API_TOKEN;
  const path = `/articles`;
  const options = { headers: { Authorization: `Bearer ${token}` } };
  const articleResponse = await fetchAPI(
    path,
    {
      populate: ["category"],
    },
    options
  );

  return articleResponse.data.map(
    (article: {
      attributes: {
        slug: string;
        category: {
          slug: string;
        };
      };
    }) => ({ slug: article.attributes.slug, category: article.attributes.slug })
  );
}
```

`layout.tsx` serves as a wrapper for the content rendered on the article page by its child components. It has the following functions:

- `fetchSideMenuData`: Fetches data (`articles` and `categories`) for the side menu using the `fetchAPI`.
- `LayoutRoute`: Makes use of the `fetchSideMenuData` function to fetch the necessary data based on the `category` received from the `params`. It then renders the content within a section container with padding and spacing. The layout is divided into two columns using CSS grid: the first column contains the `children` components, and the second column contains the `ArticleSelect` component, which receives the fetched `categories`, `articles`, and `params` as props.
- `generateStaticParams`: Retrieves article data from the backend, including the article's `slug` and its associated `category`. It returns an array of objects containing the `slug` and `category` for each article.

### Add `ArticleSelect` component

Create a file named `ArticleSelect.tsx` inside the `components` folder:

```bash
touch src/app/[lang]/components/ArticleSelect.tsx
```

Add the following code to `ArticleSelect.tsx`:

```tsx
import React from "react";
import Link from "next/link";

interface Category {
  id: number;
  attributes: {
    name: string;
    slug: string;
    articles: {
      data: Array<{}>;
    };
  };
}

interface Article {
  id: number;
  attributes: {
    title: string;
    slug: string;
  };
}

function selectedFilter(current: string, selected: string) {
  return current === selected
    ? "px-3 py-1 rounded-lg hover:underline dark:bg-violet-700 dark:text-gray-100"
    : "px-3 py-1 rounded-lg hover:underline dark:bg-violet-400 dark:text-gray-900";
}

export default function ArticleSelect({
  categories,
  articles,
  params,
}: {
  categories: Category[];
  articles: Article[];
  params: {
    slug: string;
    category: string;
  };
}) {

  return (
    <div className="p-4 rounded-lg dark:bg-gray-900 min-h-[365px] relative">
      <h4 className="text-xl font-semibold">Browse By Category</h4>

      <div>
        <div className="flex flex-wrap py-6 space-x-2 dark:border-gray-400">
          {categories.map((category: Category) => {
            if (category.attributes.articles.data.length === 0) return null;
            return (
              <Link
                href={`/${category.attributes.slug}`}
                className={selectedFilter(
                  category.attributes.slug,
                  params.category
                )}
              >
                #{category.attributes.name}
              </Link>
            );
          })}
          <Link href={"/"} className={selectedFilter("", "filter")}>
            #all
          </Link>
        </div>

        <div className="space-y-2">
          <h4 className="text-lg font-semibold">Other Posts You May Like</h4>
          <ul className="ml-4 space-y-1 list-disc">
            {articles.map((article: Article) => {
              return (
                <li>
                  <Link
                    rel="noopener noreferrer"
                    href={`/${params.category}/${article.attributes.slug}`}
                    className={`${
                      params.slug === article.attributes.slug &&
                      "text-violet-400"
                    }  hover:underline hover:text-violet-400 transition-colors duration-200`}
                  >
                    {article.attributes.title}
                  </Link>
                </li>
              );
            })}
          </ul>
        </div>
      </div>
    </div>
  );
}
```

This component is responsible for rendering a side menu that allows users to browse articles by category and displays a list of related articles. It displays category links and a list of related articles.

There is a helper function called `selectedFilter` that determines the CSS class based on the currently selected `category`.

A side menu section is rendered with the title "Browse By Category". It then iterates over the `categories` array and renders links for each `category`. If a `category` has no associated articles, it will be skipped. The link's appearance is determined by the `selectedFilter` function.

At the end of the side menu, there is a section titled "Other Posts You May Like". It renders a list of related articles based on the `articles` array. Each article is rendered as a list item with a link to the corresponding article's URL.

### Test Article Page

Make sure your Strapi backend server is running, then start your Next.js frontend development server.

Run the following command in the `frontend` directory:

```bash
yarn dev
```

Visit `http://localhost:3000` in your browser and you should see the blog home page. Click on any one of the articles and you should see a full article.

![Single Article Page](https://i.imgur.com/v7auyX2.png)

## Conclusion

In conclusion, this article provided a comprehensive guide on building a blog website using Strapi for the backend and Next.js for the frontend. The tutorial covered various steps, including setting up the project folder, creating a Strapi app, seeding the data, setting up the Next.js app, configuring the Strapi API token, setting up middleware, fetching data from Strapi, creating the home page for the blog, creating category pages and creating a single article page.

By following the step-by-step instructions and code examples, you learned how to leverage the power of Strapi and Next.js to create a fully functional blog website. They learned how to fetch data from the Strapi API, display articles in a neat blog-like format, implement pagination, and handle internationalization.

Overall, this tutorial gave developers the necessary knowledge and resources to build their own blog website using Next.js and Strapi. By combining the flexibility of Strapi's CMS with the powerful frontend capabilities of Next.js, you can create dynamic and customizable blogs tailored to their specific needs.
















