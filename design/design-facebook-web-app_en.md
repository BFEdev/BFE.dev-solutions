> First of all, there is no absolute right or wrong answer, the interviewers are not even expecting one. Rather, they are evaluating **the process of how you derive a solution to an open question**. You better treat the interview like a real discussion with your future colleague. Our solution here is just a rough example.

> We suggest you create your own system design template that you feel comfortable with to tackle all kinds of system design questions.

## Outline

In this question, we try to fill the 40-min session with below areas.

1. MVP Scope (2 min)
2. Overall System Architecture (5 min)
3. Client-Side Component Structure (5 min)
4. API Interface (5 min)
5. Key Challenges (20 min)

## 1. MVP Scope (2 min)

[facebook.com](https://facebook.com) is a very complex web app, let's focus on following MVP(Minimum Viable Product) scope.

1. a news feed that renders posts(text, photo)
2. create new post
3. scrolls down to load more posts
4. navigation to detail page to show more info
5. interactions: like, comment .etc.

## 2. Overall System Architecture (5 min)

As we focus on Front End, we don't need to dive into too much details of Back End architecture, but it is still useful to draw an overall system architecture diagram.

https://bigfrontend.dev/tools/excalidraw/embed/b73dca9a-cfe6-4a5c-a83b-d393b28e9af7?w=900&h=900

## 3. Client-Side Component Structure (5 min)

https://bigfrontend.dev/tools/excalidraw/embed/16b6c731-7f51-4573-965b-07b35c1baca3?w=938&h=717

Following above sketch, it's reasonable to have following components.

1. PostList: renders posts and handle pagination
2. Composer: create a new post
3. PostItem: renders post content based on their types
4. PostDetail: renders more info about a post
5. LikeButton: renders like count and enables like and unlike.

## 4. API Interface (5 min)

Let's use TypeScript to define the fields easier.

```ts
type Image = {
  type: 'image'
  src: string
}

type Video = {
  type: 'video'
  src: string
  thumbnail: string
}

type Attachment = Image | Video

type LikeData = {
  count: number
  liked: boolean
}

type AuthorData = {
  username: string
  avatar: string
}

// Post and Comments are not that different
// we can use a type to differentiate them

type PostType = 'post' | 'comment'

type Post<T extends PostType = 'post'> = {
  type: T
  id: string
  author: AuthorData
  body?: string
  attachments: Array<Attachment>
  like: LikeData
  comments: Array<Post<'comment'>>
  replyTo: string
}
```

For data fetching we need endpoints that enable us to do follow actions. Notice our choice on data fetching framework solutions determines how we interact with data, here we just explain it abstractly.

```ts
// with cursor-based pagination
type getPostList = (params: {
  limit: number
  from?: string
}) => Promise<Array<Post>>

type upload = (params: { type: Attachment['type']; blob: Blob }) => Promise<{
  type: Attachment['type']
  id: string
}>

type createPost = (params: {
  body?: string
  attachments: Array<{
    type: Attachment['type']
    id: string
  }>
}) => Promise<Post>

type getPostDetail = (param: { postId: string }) => Promise<Post>

type sendLike = (params: {
  postId: string
  liked: boolean
}) => Promise<LikeData>
```

## 5. Key Challenges (20 min)

Let's briefly touch each key challenge and only dives in when interviews want more.

### 5.1. Pagination

Since for news feed, usually we scroll down to the bottom to load more posts, which means a cursor-based pagination suits better than page-based one.

```ts
type getPostList = (params: {
  limit: number
  from?: string
}) => Promise<Array<Post>>
```

1. We can add `hasNext: boolean` field in the response to indicate whether we should load more, or simply check if returned list is empty.
2. To trigger the loading of next page, we can use [Intersection Observer API](https://developer.mozilla.org/en-US/docs/Web/API/Intersection_Observer_API), which is much more performant than hijacking scroll event. Proper buffer space is needed to create the smooth experience.

After scrolling for a few times, there could be too many DOM nodes being created, we can switch to traditional pagination or use following List Virtualization to address this issue.

### 5.2. List Virtualization

This is simply the idea of sliding window to **only render the DOM needed in the viewport**. There is also a question on BFE.dev for this - [Design an Infinite Scroller](https://bigfrontend.dev/design/Designa-an-Infinite-Scroller).

The tricky part is to avoid flickering, which means we need to replace the invisible DOM nodes on top with an empty node with calculated height.

Don't try to implement manually, use the existing libraries.

### 5.3. Realtime update for new post

While user are constantly scrolling the feed, we need a way to notify users that there is new post, since abruptly inserting it on top would lead to UI glitches.

1. Set up [SSE(Server-Sent Event)](https://bigfrontend.dev/question/Long-Polling-vs-Sever-Sent-Event-vs-WebSocket) to receive new post data
2. When there is new post, display "new post" toast on top
3. When "new post" toast is tapped, scroll to top and render the latest posts.

### 5.4. Realtime update for like, comment

For trending posts from users with many followers, the numbers on the post might be constantly changing, we can use the similar pattern as above.

We can try to use just one SSE connection to handle all the real time update requirements. With an [Event Emitter](https://bigfrontend.dev/problem/create-an-Event-Emitter), the component can subscribe when mounted and unsubscribe when unmounted.

### 5.5. Handling images

Following this question - [155. how to improve performance of a webpage of a lot of images?](<https://bigfrontend.dev/question/how-to-improve-webpage-of-many-images()>), there are many ways to improve the performance, roughly including


1. **cropping**: generate thumbnails of different sizes and use the best matched ones.
2. **encoding**: choose a better format like [AVIF](https://www.smashingmagazine.com/2021/09/modern-image-formats-avif-webp/#avif) and [WebP](https://www.smashingmagazine.com/2021/09/modern-image-formats-avif-webp/#webp).
3. **loading**: lazy loading with `loading="lazy"`
4. **transferring**: choose proper cache through CDN network.
5. **rendering**: use proper `srcset` and `sizes` on [&lt;img&gt; tag](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/img#attr-sizes).

### 5.6. Render different post types

There could be many post types besides text and images, even for same post it could be rendered differently based on where it is used.

Putting everything together could easily bloat the JavaScript bundle. With [Code Splitting](https://webpack.js.org/guides/code-splitting/), we can have common ones built-int, but lazy load the other rendering resources.

### 5.7. Skeleton Screens

When we got to post detail page, in order to have a better UX, we can use [skeleton screens](https://bigfrontend.dev/question/What-is-Skeleton-Screen-How-to-implement-them) to give users a hint about what's to come, it could potentially improve the [perceived performance](https://developer.mozilla.org/en-US/docs/Learn/Performance/Perceived_performance).

We should not use this pattern excessively though.

### 5.8. Pre-load / Pre-fill data on navigation

On page navigation, we can even start data & resource loading earlier rather than wait for the mounting of components.

1. Start loading data & resources when users hover on the post card.
2. Since there are data on post list that could be reused on the post detail, we can pre-fill the data by passing it along in the url or in `location.state`.

Above 2 is just really an idea, we should rely on data-fetching framework to do it for us. For example, Relay has built-in support for [Rendering Partially Cached Data](https://relay.dev/docs/guided-tour/reusing-cached-data/rendering-partially-cached-data/), and SWR has [fallbackDat Option to pre-fill](https://swr.vercel.app/docs/prefetching#pre-fill-data).

### 5.9. Optimistic UI

When we click on the Like button, the round-trip of API call might take some time to finish. Since most of the time the request should succeed, we can assume it will succeed and update the component right away and only revert the change when failed.

Again we should rely on data-fetching library to do it for us. SWR has [optimisticData for Optimistic UI](https://swr.vercel.app/blog/swr-v2#optimistic-ui).

### 5.10. How to attach the images and videos

Image and Video processing takes a lot of time and resources, we need to be careful to orchestrate the steps to achieve smooth UX.

1. When user choose some files on composer, we can start uploading the file.
2. Server receives the binary and returns a media `id` after dispatching an asynchronous job to generate thumbnails .etc.
3. When Post button is clicked, create the post with the media `id`.
4. During rendering, check the progress of the process and render appropriate UI.
