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

Delete the following auto-generated files in your `frontend` directory as we will not need them:
- `src/app/favicon.ico`
- `src/app/layout.tsx`
- `src/app/page.tsx`
- `src/app/globals.css`

## Step 5: Set up Strapi API Tokens

Create a `.env` file in the root of your `frontend` directory and paste the following environment variables:

```
NEXT_PUBLIC_STRAPI_API_TOKEN=your-api-token
NEXT_PUBLIC_PAGE_LIMIT=6
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

Your updated `.env` file should have environment variables similar to this:

```
NEXT_PUBLIC_STRAPI_API_TOKEN=ab2bfc3f905e61b58cbb76c1d459609ac29351249f5ffb98db9dd6d109ab60d651de15e0d31e3e367a754e622fe3c918fef89f0c28ae7b9c6e788d3db4d2ea7abd7b282b37b8448886c865607965ea82c57648e11de6bb278c7bbac811051287dbf6657cac0604716fd4d4501771db43b36b531b8418ca5c8a4bc248eb85b266
NEXT_PUBLIC_PAGE_LIMIT=6
NEXT_PUBLIC_STRAPI_API_URL=http://localhost:1337
```

Of course, the `NEXT_PUBLIC_STRAPI_API_TOKEN` for your Strapi server app will be different from the one shown above.

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

Before the data request from the Strapi backend is completed, middleware is required to handle certain tasks in a Next.js frontend app.

Create a new file named `middleware.ts` in the `frontend/src` directory.

```bash
touch frontend/src/middleware.ts
```

The `middleware.ts` file serves the purpose of adding internationalization (i18n) support to the application. It ensures that the URL contains a locale segment and redirects the user to the appropriate URL if a locale is missing. Additionally, it determines the best-matched locale based on the request headers using language negotiation and the available locales.

>*Find out more about Next.js middleware on the [Routing: Middleware | Next.js](https://nextjs.org/docs/pages/building-your-application/routing/middleware) page.*

Open up `middleware.ts` and add the following code:

```tsx
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

## Step 7: Fetch data from Strapi

In this step we will create two files, `api-helpers.ts` and `fetch-api.tsx` to fetch data from the Strapi API backend.

In your `frontend/src/app` directory, create a new directory named `[lang]` for handling the locales we defined in `i18n-config.ts`.

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

## Step 8: Create Home Page for blog

In this step, you will create a home page for your blog frontend. The home page in this case will display a list of all the articles.

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

Notice that the `page.tsx` file imports a `Loader`, `PostList`, and `PageHeader` components, but these are yet to be created. Let's create the files.

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

```bash
yarn dev
```

Visit `http://localhost:3000` in your browser and you should see the home page of your blog.

![Blog Home Page]()

Great! The home page is working and is displaying a grid of all posts as expected. 

The next step is to create a page for each post. The pages will display the full article.







