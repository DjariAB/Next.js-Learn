## Some notes i've wrote down while following the course
### CSS Styling 
the different ways of CSS styling in next.js
#### Global styles
	put in the "global.css" file inside /app/ui 
used to add CSS rules to all the routes in the app, it can be imported in any component
```tsx
import '@/app/ui/global.css';
```
but ==it's a good practice to add it to your top level component ("root layout" in next.js)==
#### Tailwind 
Tailwind is completely compatible with next.js
#### CSS modules 
allow you to to scope CSS to a component by automatically creating unique class names, starts by declaring a CSS class in your "name.module.css" file
```css
.shape 
{ height: 0; width: 0; border-bottom: 30px solid black; border-left: 20px solid transparent; border-right: 20px solid transparent;}
```
then importing it to your component and applying your styles 
```tsx 
import styles from '@/app/ui/home.module.css';
<div className={styles.shape} />
```
#### Clsx library
can be used to toggle classnames for conditional styling, [documentaion](https://github.com/lukeed/clsx)
basic e.g : 
```tsx 
import clsx from 'clsx';
<span className={clsx( 'inline-flex items-center rounded-full px-2 py-1 text-sm', 
{ 
	'bg-gray-100 text-gray-500': status === 'pending',
	'bg-green-500 text-white': status === 'paid', }, 

					 )} >
```

### Fonts
next.js automatically optimizes fonts by downloading font files at build time which means they will be hosted with other static assets resulting no additional network requests and less layout shifts.
#### Adding a primary font
- create your `fonts.ts` file in the `/app/ui` directory 
- import your next font and specify what subset and weights you'd like to load 
	```tsx 
	import { Inter } from 'next/font/google'; 
	export const inter = Inter({weight : ["400" , '700'],
	 subsets: ['latin'] });
	```
- finally import your font to your component and apply it as a className
	```tsx
	import { inter } from '@/app/ui/fonts';
	<body className={`${inter.className} antialiased
	`}>{children}</body>
	```
### Images
next.js `<Image />` component ensures your image assets are optimized and responsive on different screen sizes.
it is a good practice to set the `width` and `height` of your images to avoid layout shift, ==these should be an aspect ratio **identical** to the source image.==
### Nested routing
Next.js uses file-system routing where folders are used to create nested routes
![[Pasted image 20240825112505.png]]
`page.tsx` is a special Next.js file that exports a React component, and it's required for the route to be accessible. In your application, 

> To create a nested route, you can nest folders inside each other and add `page.tsx`
> ![[Pasted image 20240825114806.png]]

#### Colocation 
you have the option to colocate your own files (e.g. components, styles, tests, etc) inside folders in the `app` directory.
- Only the content inside the `page` file will be publicly accessible.
#### Layouts
layouts or `layout.tsx` are special files used to create UI that is shared between multiple pages.
The `<Layout />` component receives a `children` prop. This child can either be a page or another layout.
```tsx
export default function Layout({ children }: { children: React.ReactNode }) {
...
```
- One benefit of using layouts in Next.js is that on navigation, only the page components update while the layout won't re-render. This is called [partial rendering](https://nextjs.org/docs/app/building-your-application/routing/linking-and-navigating#4-partial-rendering)

> Since the layout UI will be shared across all descending routes, ==the root layout== : `/app/layout.jsx` is required and used to set app shared UI, your `<html>` and `<body>` tags and metadata.
### Navigation
#### The `<Link>` component
navigation is optimized in Next.js when using the `<Link />` component because On the server, your application code is automatically [code-split](https://nextjs.org/docs/app/building-your-application/routing/linking-and-navigating#1-code-splitting) by route segments. And on the client, Next.js [prefetches](https://nextjs.org/docs/app/building-your-application/routing/linking-and-navigating#2-prefetching) and [caches](https://nextjs.org/docs/app/building-your-application/routing/linking-and-navigating#3-caching) the route segments. This means, when a user navigates to a new route, the browser doesn't reload the page, and only the route segments that change re-render.

> in production, whenever [`<Link>`](https://nextjs.org/docs/api-reference/next/link) components appear in the browser's viewport, Next.js automatically **prefetches** the code for the linked route in the background.

### Data Fetching
#### Parallel data fetching
is a common way to avoid waterfalls by initiating all data requests at the same time - in parallel. and it can be done using the [`Promise.all()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/all) or [`Promise.allSettled()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/allSettled) functions to initiate all promises at the same time.
### Terms
#### Route Handlers
Route Handlers are functions that are executed when users access site routes. They’re responsible for handling incoming HTTP requests for the defined URLs or routes to produce the required data.
#### Request waterfalls
A "waterfall" refers to a sequence of network requests that depend on the completion of previous requests. In the case of data fetching, each request can only begin once the previous request has returned data.
### Good to know 
- By default, Next.js applications use **React Server Components**.