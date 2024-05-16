# Next cheatsheet

JavaScript framework based on react library.

![preview img](/preview.jpg)

## History
    
- Initial release: 25 oct 2016
- Founder/Founders: Guillermo Rauch
- Developers: Vercel/Nextjs community
- Goals: provide critical features that React lacks, primarily in terms of speed and SEO optimization

## Prerequisites
    
react: UI-library/View-engine based on javascript and xml.

## Development tools

- VSCode
- VSCode extensions: React simple snippets/ES6 ...
- NodeJS
- NextJS dependencies
    
## How to create

```npm
npx create-next-app@latest
```

## Variants

- javascript
- typescript

## Components

a function that return piece of UI.

- components > Button.tsx
- components > Alert.tsx
- components > Modal.tsx
- ...

## Server components

- render on server no need hydration
- fetching data
- access backend resources
- use sensitive data (token, api keys and ...)
- keep large dependencies on the server

## Client components

- render on server and hydrated in the browser
- create form (search, signin/signup, admin pages and ...)
- use hooks and events (onScroll, onClick, onSubmit and ...)
- access browser api (localstorage, geolocation and ...)

## Next topics
    
### Client

app > tickets > create > CreateForm.tsx

```js
"use client"

import { useRouter } from "next/navigation";
const router = useRouter();

const handleSubmit = async (e) => {
  ...
  if(res.status === 201) {
    router.refresh();
    router.push("/tickets");
  }
  ...
}
```

#### Link
        
```js
"use client"

import Link from "next/link";
import { usePathname } from 'next/navigation'

const pathName = usePathname();

<Link href="/" className={`${pathName === "about" ? active : nonActive}`}> Home </Link>
```

#### Font
        
```js
import { Inter } from "next/font/google";
const inter = Inter({ subsets: ["latin"] });

<div className={inter.className}> ... </div>
```

### Server
      
#### Route

create path for a page

- app > page.tsx - url/
- app > about > page.(jsx|tsx) - url/about
- app > news > latest > page.(jsx|tsx) - url/news/latest
      
#### Layout

define local and global layouts for repetitive piece of UI.
        
- app > layout.tsx - define global layout
- app > about > layout.tsx - define specific layout for about page along side with root layout
- app > (admin) > layout.tsx - define specific layout for grouped pages along side with root layout

```js
type ChildrenType = {
  children: React.ReactNode
}

function RootLayout = ({ children }: ChildrenType) => {
  return (<html><body> {children} </body></html>)
}

export default RootLayout;
```
     
#### MetaData

```js
import type { Metadata } from "next";

export const metadata: Metadata = { title: "...", description: "..." }
```

#### Tailwind

```css
.card {
  @apply <tailwind classes>
}

.card {
  @apply 
    <tailwind classes>
    ...
}
```
     
#### Image
       
```js
<Image src={LogoImg} alt="" width={128} height={128} quality={100} placeholder="blur" />
```

##### Use Image from external source

next.config.js >

```js
const nextConfig = {
  images: {
    remotePatterns: [
      {
        protocol: "https",
        hostname: "shutter-stock.com"
      }
    ]
  }
};
```

#### Json-Server

- data > db.json > { "tickets": [ { ... }, ... ] }
- npm i -g json-server
- json-server --watch --port 3600 ./data/db.json
- url:3600/tickets

#### Actions

```js
'use client'
 
import { useFormStatus } from 'react-dom';
import { revalidatePath } from "next/cache";

const addCommentForm = () => {
  const { pending } = useFormStatus();

  const addComment = async (formData: FormData) => {
    "use server"

    const comment = {
      title: formData.get("title") as string,
      body: formData.get("body") as string
    }

    // use orm to add comment

    revalidatePath("/comments");
  }

  return (<form action={addComment}>
    <input name="title" />
    <input name="body" />
    <button disabled={pending}> Add </button>
  </form>);
}

```
      
#### Static and Dynamic rendering

- In order to give data in this specific fetch request (dev: dynamic, prod: static)

```js
const response = await fetch("url");
```

- In order to give fresh data in every fetch request in route level (prod: dynamic)

```js
export const dynamic = "force-dynamic";

async function CommentsPage() {
   ...
}

export default CommentsPage;
```

- In order to give fresh data in this specific fetch request (prod: dynamic)

```js
const response = await fetch("url", { cache: "no-cache"  })
```

- In order to give fresh data after every 30 seconds (prod: cache + dynamic)
 
```js      
async function getTickets() {
  const res = await fetch("url", { next: { revalidate: 30 } });
  return res.json();
}
       
const tickets = await getTickets();

{tickets.length === 0 && <p> No ticket </p>}
{tickets.map((ticket) => (...))}
```
     
#### Dynamic segments
        
app > tickets > [id] > page.tsx - url/tickets/1
       
```js
const TicketDetailsParamsType = {
  id: string
};

export default async function TicketDetails({ params }: TicketDetailsParamsType) {
  const ticket = await getTicket(params.id);
          
  ...
}
```

#### SSR

NextJS know list of all params so statically generate all of the contents at the background.

```js
export async function generateStaticParams() {
  ...
          
  return tickets.map((ticket) => [{ id: ticket.id }])
}
```

in this situation never set revalidate property to zero.

#### NotFound

```js
import { notFound } from "next/navigation";

async function getTickets() {
  ...
  if(!res.ok) notFound();
  ...
}
```

##### Custom NotFound

define not found page when user search for url address that not exist.
  
- app > not-found.tsx - define global not found page
- app > tickets > not-found.tsx - Replaced global not found page

#### Suspense and Streaming

define loading/skeleton when page/part of the page need times to be loaded.
  
- app > loading.tsx - define global loading indicator
- app > comments > loading.tsx - replace global loading indicator 
       
```js
import Loading from "../loading";

<Suspense fallback={<Loading />}>
  <TicketList />
</Suspense>
```

#### Middleware

app > middleware.ts

```js
import { NextResponse } from "next/server";
import { NextRequest } from "next/server";

async function middleware(request: NextRequest) {
  const isAuthenticated = false;

  if(!isAuthenticated)
    return NextResponse.redirect("/login");

  return NextResponse.next();
}

export const config = [
  matcher: ["/dashboard", "/account"]
];
```

#### Environment variable

- env
- env.local
