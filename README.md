This is the project created on March 4, 2021 for the Async JavaScript meetup talk: [Fullstack Jamstack Development with RedwoodJS](https://www.youtube.com/watch?v=n1CUe6ArjD8)

> **WARNING:** RedwoodJS software has not reached a stable version 1.0 and should not be considered suitable for production use. In the "make it work; make it right; make it fast" paradigm, Redwood is in the later stages of the "make it work" phase.

## Getting Started
- [Tutorial](https://redwoodjs.com/tutorial/welcome-to-redwood): getting started and complete overview guide.
- [Docs](https://redwoodjs.com/docs/introduction): using the Redwood Router, handling assets and files, list of command-line tools, and more.
- [Redwood Community](https://community.redwoodjs.com): get help, share tips and tricks, and collaborate on everything about RedwoodJS.

### Setup

We use Yarn as our package manager. To get the dependencies installed, just do this in the root directory:

```terminal
yarn install
```

### Fire it up

```terminal
yarn redwood dev
```

Your browser should open automatically to `http://localhost:8910` to see the web app. Lambda functions run on `http://localhost:8911` and are also proxied to `http://localhost:8910/.redwood/functions/*`. 

## Script for Talk

### Install dependencies

```bash
yarn create redwood-app async-redwood
```

### Fire it up

```bash
cd async-redwood && yarn rw dev
```

### Generate home page

```bash
yarn rw generate page home /
```

```javascript
const HomePage = () => {
  return (
    <>
      <h1>Hello Async</h1>
      <p>Woot!</p>
    </>
  )
}

export default HomePage
```

### Generate about page

```bash
yarn rw generate page about
```

```javascript
const AboutPage = () => {
  return (
    <>
      <h1>About</h1>
      <p>This page tells you about stuff!</p>
    </>
  )
}

export default AboutPage
```

### Generate layout

```bash
yarn rw g layout blog
```

```javascript
import { Link, routes } from '@redwoodjs/router'

const BlogLayout = ({ children }) => {
  return (
    <>
      <header>
        <h1><Link to={routes.home()}>Hello Async</Link></h1>
        <nav>
          <ul>
            <li><Link to={routes.about()}>About</Link></li>
          </ul>
        </nav>
      </header>
      <main>{children}</main>
    </>
  )
}

export default BlogLayout
```

### Import BlogLayout into home page

```javascript
import BlogLayout from 'src/layouts/BlogLayout'

const HomePage = () => {
  return (
    <>
      <BlogLayout>
        <p>Woot!</p>
      </BlogLayout>
    </>
  )
}

export default HomePage
```

### Import BlogLayout into about page

```javascript
import BlogLayout from 'src/layouts/BlogLayout'

const AboutPage = () => {
  return (
    <>
      <BlogLayout>
        <h1>About</h1>
        <p>This page tells you about stuff!</p>
      </BlogLayout>
    </>
  )
}

export default AboutPage
```

### Create Post model in Prisma schema

```prisma
model Post {
  id        Int      @id @default(autoincrement())
  title     String
  body      String
  createdAt DateTime @default(now())
}
```

### Change database to Postgres

```prisma
datasource DS {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}
```

### Set database environment variable

```
DATABASE_URL=
```

### Migrate table

```bash
yarn rw prisma migrate dev
```

### Scaffold post interface

```bash
yarn rw g scaffold post
```

### Generate BlogPosts cell

```bash
yarn rw g cell BlogPosts
```

```javascript
export const QUERY = gql`
  query BlogPostsQuery {
    posts {
      id
    }
  }
`

export const Loading = () => <div>Loading...</div>
export const Empty = () => <div>Empty</div>
export const Failure = ({ error }) => <div>Error: {error.message}</div>

export const Success = ({ posts }) => {
  return JSON.stringify(posts)
}
```

### Import cell onto home page

```javascript
import BlogLayout from 'src/layouts/BlogLayout'
import BlogPostsCell from 'src/components/BlogPostsCell'

const HomePage = () => {
  return (
    <>
      <BlogLayout>
        <p>Woot!</p>
        <BlogPostsCell />
      </BlogLayout>
    </>
  )
}

export default HomePage
```

### Pull in all posts info

```javascript
export const QUERY = gql`
  query BlogPostsQuery {
    posts {
      id
      title
      body
      createdAt
    }
  }
`
```

### Map over posts and display post title

```javascript
export const Success = ({ posts }) => {
  return posts.map((post) => (
    <article key={post.id}>
      <header>
        <h2>{post.title}</h2>
      </header>
    </article>
  ))
}
```

### Display date and post body

```javascript
export const Success = ({ posts }) => {
  return posts.map((post) => (
    <article key={post.id}>
      <header>
        <h2>{post.title}</h2>
      </header>

      <div>{post.createdAt}</div>

      <p>{post.body}</p>
    </article>
  ))
}
```

### Setup Netlify deploy

```bash
yarn rw setup deploy netlify
```

### Initialize git repository

```bash
git init
git add .
git commit -m "Initial commit"
git branch -M main
git remote add origin https://github.com/ajcwebdev/async-redwood.git
git push -u origin main
```

### Set connection pool

```
postgres://<user>:<pass>@<url>/<db>?connection_limit=1
```
