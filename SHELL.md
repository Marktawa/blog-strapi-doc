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


Navigate into `blog-strapi`.


## Step 2: Create a standard Strapi app

Create your Strapi app in a folder named `backend`.


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


Next, import data into your backend's database.


Answer `y` to `The import will delete all assets and data in your database. Are you sure you want to proceed? (y/N)`

> **NOTE:**
>
> The data used to seed the database is from the Strapi Nextjs Starter.

After a successful import, rerun your Strapi backend server. 


In your browser, login to your admin panel. You should see the newly imported `content` and `collection types`.

![Updated Dashboard]()

## Step 4: Create a standard Next.js app

In a new terminal session, change directory to `blog-strapi` and run the following command:


On installation, you'll see some prompts. Name your project `frontend` and refer to the image below for the other responses.

![Create Next App Prompts]()

Add the following dependencies to your `frontend` Next app: `@types/negotiator`, `negotiator`, `@types/qs`, `qs`, `classnames`, `react-icons`, `react-markdown`, `react-slideshow-image`, `remark-gfm`, `@formatjs/intl-localematcher` for use later.


Move the following dependencies to devdependencies in your `frontend` Next app: `autoprefixer`, `postcss`, `tailwindcss`.


Delete the following auto-generated files in your `frontend` directory as we will not need them:
- `src/app/favicon.ico`
- `src/app/layout.tsx`
- `src/app/page.tsx`
- `src/app/globals.css`

## Step 5: Set up Strapi API Token

Create a `.env` file in the root of your `frontend` directory and paste the following environment variables:


Go back to your Strapi Admin panel to create the API token to be used for displaying content.

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


Of course, the `NEXT_PUBLIC_STRAPI_API_TOKEN` for your Strapi server app will be different from the one shown above.

## Step 6: Setup middleware

Create a `i18n-config.ts` file in the root of your `frontend` folder to set up the internationalization configuration.


Add the following code to `i18n-config.ts`:


Before the data request from the Strapi backend is completed, middleware is required to handle certain tasks in a Next.js frontend app.

Create a new file named `middleware.ts` in the `frontend/src` directory.


The `middleware.ts` file serves the purpose of adding internationalization (i18n) support to the application. It ensures that the URL contains a locale segment and redirects the user to the appropriate URL if a locale is missing. Additionally, it determines the best-matched locale based on the request headers using language negotiation and the available locales.

>*Find out more about Next.js middleware on the [Routing: Middleware | Next.js](https://nextjs.org/docs/pages/building-your-application/routing/middleware) page.*

Open up `middleware.ts` and add the following code:


The `middleware.ts ` file imports `NextRequest` and `NextResponse` types to handle incoming requests and generate responses in Next.js. It imports the `i18n` object from the `i18n-config` module we created earlier to access the available locales. The `matchLocale` function is used for matching and determining the best locale based on the request headers and available locales.

## Step 7: Fetch data from Strapi

In this step we will create two files, `api-helpers.ts` and `fetch-api.tsx` to fetch data from the Strapi API backend.

In your `frontend/src/app` directory, create a new directory named `[lang]` for handling the locales we defined in `i18n-config.ts`. This handled through Next.js' [dynamic routes](https://nextjs.org/docs/pages/building-your-application/routing/dynamic-routes) feature.


In your `frontend/src/app/[lang]` directory, create a new directory and name it `utils`. Execute these commands in the root of your `frontend` folder.


In the `utils` folder, create `api-helpers.ts` and `fetch-api.tsx`.


Add the following code to `api-helpers.ts`:


`api-helpers.ts` defines two helper functions: `getStrapiURL` and `getStrapiMedia`. 

- `getStrapiURL`: This function returns the Strapi API URL. If the `NEXT_PUBLIC_STRAPI_API_URL` environment variable is set it will use the value as a base URL otherwise it defaults to `http://localhost:1337`. It takes an optional `path` parameter that represents a path to a specific endpoint. It concatenates the base URL with the provided `path` and returns the complete URL.

- `getStrapiMedia`: This function returns the correct URL for media regardless of whether the media is hosted externally or within the Strapi API.

These helper functions provide a convenient way to handle the Strapi API URL and media URLs for our frontend app.

Add the following code to `fetch-api.tsx`:


The provided code defines a helper function named `fetchAPI` that performs an API call using the [JavaScript Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API).

The code imports the `qs` library which provides query string parsing and formatting functionality. It also imports the `getStrapiURL` helper function from the `api-helpers.ts` module we created previously.

The `fetchAPI` functions takes three parameters: `path`, `urlParamsObject`, and `options`. `path` is the API endpoint path, `urlParamsObject` is an object containing URL parameters, and `options` is an object containing additional options for the API call.

The `requestUrl` is constructed by combining the Strapi API URL obtained from the `getStrapiURL` function with the provided `path` and any query string parameters present in the `urlParamsObject`. The `qs.stringify` function converts the `urlParamsObject` into a query string.

The API call is triggered using the `fetch` function passing in the `requestUrl` and `mergedOptions`. What is returned is `data` in JSON format.

Add the following code to `next.config.js` in the `frontend` directory to access both local and remote media sources but still use the built-in [Next.js Image Optimization API](https://nextjs.org/docs/pages/building-your-application/optimizing/images)


## Step 8: Create Home Page for blog

In this step, you will create a home page for your blog frontend. The home page in this case will display a list of all the articles.

### Add `page.tsx` file

Create a new file named `page.tsx` in the `src/app/[lang]` directory.


Add the following code to `page.tsx`:


This code sets up a Next.js page that will fetch a list of all the articles from the Strapi API `/articles`endpoint and renders them in a neat blog-like format. It includes pagination for loading more posts when the `Load more posts` button is clicked.

The `fetchData` function uses the `fetchAPI` function to make the API request. It retrieves the `NEXT_PUBLIC_STRAPI_API_TOKEN` from the environment variables to construct the request path and URL parameters.

Notice that the `page.tsx` file imports a `Loader`, a `PostList`, and a `PageHeader` components, but these are yet to be created. Let's create the files.

Create a `components` folder in the `src/app/[lang]/` folder to store all your components:


### Add Loader Component

Add a Loader component to provide visual feedback to the user that data is still being fetched from the API.

Create a new file named `Loader.tsx` in the `components` folder and add the following code to `Loader.tsx`


### Add PostList Component

The `PostList` component will render a grid of articles and retrieve the necessary attributes like the `title`, `description`, `slug`, `cover`, and `author` for each article.

Create a new file named `PostList.tsx` in the `components` folder and add the following code to `PostList.tsx`


The `PostList` component provides a reusable way to display a list of blog posts with consistent styling and structure.

### Add PageHeader Component

The `PageHeader` component will add a reusable header to your frontend.

Create a new file named `PageHeader.tsx` in the `components` folder and add the following code to `PageHeader.tsx`.


### Create Layout

Let's create a layout that provides code for the UI. This will be shared between routes.

Create a new file named `layout.tsx` in the `src/app/[lang]` directory.


Add the following code to `layout.tsx`:


The `layout.tsx` provides a consistent layout structure for the blog frontend. It fetches global data from the Strapi API, retrieves metadata, and renders the necessary components such as the `Navbar` and the `Footer`. It also handles internationalization by generating static parameters based on the available locales.

Notice that the code imports a `globals.css` file, a `Footer` component, and a `Navbar` component but they are yet to be created. In addition, a `Logo` component for the blog logo will be needed. This will be used by the `Navbar` and `Footer` components. Let's create the files.

### Add Logo component

Create a new file named `Logo.tsx` in the `components` folder and add the following code to `Logo.tsx`:


### Add Navbar component

Create a new file named `Navbar.tsx` in the `components` folder and add the following code to `Navbar.tsx`:


### Add Footer component

Create a new file named `Footer.tsx` in the `components` folder and add the following code to `Footer.tsx`:


### Add `globals.css`

Create a new file called `globals.css` in the `[lang]` folder and add the following code to `globals.css`:


### Test Home Page

Make sure your Strapi backend server is running, then start your Next.js frontend development server.

Run the following command in the `frontend` directory:


Visit `http://localhost:3000` in your browser and you should see the home page of your blog.

![Blog Home Page]()

Great! The home page is working and is displaying a grid of all posts as expected. 

## Step 9: Create Category Page

The next step is to create a page for each category.

### Create `[category]` folder

Create a folder named `[category]` inside the `[lang]` folder. Since we have multiple categories we will use [Dynamic Segments](https://nextjs.org/docs/pages/building-your-application/routing/dynamic-routes#convention) for handling the routes for each category. Therefore the URL for each category will be based on its name.


### Add `page.tsx` file

Create a file called `page.tsx` in the `[category]` folder.


Add the following code to `page.tsx`:


The provided code fetches and renders posts for a specific category. It exports two asynchronous functions: `CategoryRoute` and `generateStaticParams`.

The `CategoryRoute` function handles rendering content for a specific category. It retrieves the category from the provided parameters, calls the `fetchPostsByCategory` function to fetch posts data for that category, and then renders a page header with the category name and description, followed by a list of posts.

The `fetchPostsByCategory` function fetches posts by a given category. It constructs an API request with sorting, filtering, and population options, and retrieves the response data.

The `generateStaticParams` function is used for generating static paths based on dynamic routes. In this case, it returns an empty array.

Overall, the code retrieves and displays posts data for a specific category, utilizing asynchronous functions for API requests and rendering.

### Test Category Page

Make sure your Strapi backend server is running, then start your Next.js frontend development server.

Run the following command in the `frontend` directory:


Visit `http://localhost:3000/strapi` in your browser and you should see the `strapi` category page of your blog.

![Strapi Category Page]()

You can test the other categories by clicking on the `category` links in the footer of your blog.

![Category Links]()

The category page is working and is displaying a grid of posts related to the specific category. 

## Step 10: Create Article Page

The next step is to create a page for each article which displays the full post.

### Create `[slug]` folder

Create a folder called `[slug]` in the `[category]` folder. This implies that each article will be have a route based on its `slug` as defined in the Strapi Admin Panel. The `slug` is generated from the post `title`.


Inside the `[slug]` folder, create two new files:
- `page.tsx` for fetching and rendering the article.
- `layout.tsx` for the layout of the Article page.


### Add `page.tsx` file

Add the following code to `src/app/[lang]/[category]/[slug]/page.tsx`:


The `page.tsx` file for individual posts displays an article by fetching post data using the `fetchAPI` helper function and uses the `Post` component to render the page.

`page.tsx` includes the following functions:

- `getPostBySlug`: Fetches post data based on a given slug, including `cover`, `authorsBio`, and `category`.
- `getMetaData`: Fetches metadata for a post based on a given slug.
- `generateMetadata`: Generates metadata for a post using the `getMetadata` function and returns the `title` and `description` of a post.
- `PostRoute`: Renders the post page based on a given `slug`. The function retrieves post data using the `getPostBySlug` function and renders the post using the `Post` component.
- `generateStaticParams`: Generates static parameters for articles by fetching the articles' data and mapping it to an array with the `slug` and `category` attributes as strings.

### Add `Post` component

`page.tsx` imports a `Post` component for rendering posts. Create a `Post.tsx` file in the `components` folder.


Add the following code to `Post.tsx`:


The `Post` component renders details of an article. It expects a `data` prop with specific attributes which include a `title`, `description`, `slug`, `cover`, `authorsBio`, `blocks`, and `publishedAt`. It renders an article based on these attributes.

It maps over the `data.attributes.blocks` array and uses a `postFenderer` utility function to render each section of the article. 

### Create `postRenderer` utility

Let's create the `postRenderer` utility function to be used by the `Post` component.

Create a new file named `post-renderer.tsx` inside the `utils` folder.


Add the following code to `post-renderer.tsx`:


The `postRenderer` utility function is used to dynamically render different types of sections within a post based on the `__component` value of each section. This could be a `RichText`, `ImageSlider`, `Quote`, `Media`, or `VideoEmbed` component

Next, we will create the respective components required by the `postRenderer` utility.

Create the `RichText`, `ImageSlider`, `Quote`, `Media`, and `VideoEmbed` components:


### Add `RichText` Component

Add the following code to `RichText.tsx`:


The `RichText` component converts markdown-formatted text into HTML and renders it as a section of rich text content. It relies on the `Markdown` component from the `react-markdown` library and the `remark-gfm` plugin for rendering Markdown syntax with additional features like tables.

### Add `ImageSlider` component

Add the following code to `ImageSlider.tsx`:


The `Slideshow` component creates an image slider by rendering a series of images based on the provided data. The `Fade` component from the `react-slideshow-image` library provides the sliding effect, and the `Image` component from Next.js handles the rendering of each image.

### Add `Quote` component

Add the following code to `Quote.tsx`:


The `Quote` component renders a blockquote with a `title`, `body`, and an optional `author`. The quote symbols are displayed using SVG icons, and appropriate styling classes are applied for visual presentation.

### Add `Media` component

Add the following code to `Media.tsx`:


The `Media` component renders an image or media file using the Next.js `Image` component. The image is displayed with rounded corners and is set to a specific width and height. The `getStrapiMedia` function is used to retrieve the media URL.

### Add `VideoEmbed` component

Add the following code to `VideoEmbed.tsx`:


The `VideoEmbed` component renders an embedded video based on the provided URL. It supports YouTube videos by extracting the video ID and creating an embed URL. If the URL is invalid, it displays an error message. The embedded video is displayed within a container with specified dimensions, and the iframe allows for various video playback features.

### Add Layout

Add the following code to `src/app/[lang]/[category]/[slug]/layout.tsx`:


`layout.tsx` serves as a wrapper for the content to be rendered on the article page by its child components. It has the following functions:

- `fetchSideMenuData`: Fetches data (`articles` and `categories`) for the side menu using the `fetchAPI`.
- `LayoutRoute`: Makes use of the `fetchSideMenuData` function to fetch the necessary data based on the `category` received from the `params`. It then renders the content within a section container with padding and spacing. The layout is divided into two columns using CSS grid: the first column contains the `children` components, and the second column contains the `ArticleSelect` component, which receives the fetched `categories`, `articles`, and `params` as props.
- `generateStaticParams`: Retrieves article data from the backend, including the article's `slug` and its associated `category`. It returns an array of objects containing the `slug` and `category` for each article.

### Add `ArticleSelect` component

Create a file named `ArticleSelect.tsx` inside the `components` folder:


Add the following code to `ArticleSelect.tsx`:


This component is responsible for rendering a side menu that allows users to browse articles by category and displays a list of related articles. It displays category links and a list of related articles.

There is a helper function called `selectedFilter` that determines the CSS class based on the currently selected `category`.

A side menu section is rendered with a title "Browse By Category". It then iterates over the `categories` array and renders links for each `category`. If a `category` has no associated articles, it will be skipped. The link's appearance is determined by the `selectedFilter` function.

At the end of the side menu, there is a section titled "Other Posts You May Like". It renders a list of related articles based on the `articles` array. Each article is rendered as a list item with a link to the corresponding article's URL.

### Test Article Page

Make sure your Strapi backend server is running, then start your Next.js frontend development server.

Run the following command in the `frontend` directory:

Visit `http://localhost:3000` in your browser and you should see the blog home page. Click on any one of the articles and you should see a full article.

![Single Article Page]()

## Conclusion

In conclusion, this article provided a comprehensive guide on building a blog website using Strapi for the backend and Next.js for the frontend. The tutorial covered various steps, including setting up the project folder, creating a Strapi app, seeding the data, setting up Next.js app, configuring Strapi API token, setting up middleware, fetching data from Strapi, creating the home page for the blog, creating category pages and creating a single article page.

By following the step-by-step instructions and code examples, readers learned how to leverage the power of Strapi and Next.js to create a fully functional blog website. They learned how to fetch data from the Strapi API, display articles in a neat blog-like format, implement pagination, and handle internationalization.

Overall, this tutorial provided developers with the necessary knowledge and resources to build their own blog website using the Next.js and Strapi technologies. By combining the flexibility of Strapi's content management system with the powerful frontend capabilities of Next.js, developers can create dynamic and customizable blogs tailored to their specific need