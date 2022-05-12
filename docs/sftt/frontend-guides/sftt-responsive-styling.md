# Responsive Styling in React

## The Basics
How do we change our site to look good on all devices and screen sizes? There are three main 
techniques:

1. Use flex and relative dimensions
2. Rendering different content for different screen sizes
3. Changing the style of content based on the screen size

For techniques 2-3, we need to know the size of the user's screen. In our application, 
we use the screen's *width* to determine if the user is on a mobile device, tablet, narrow desktop, 
or desktop. These options are represented by the `WindowTypes` enum:
```ts
export enum WindowTypes {
  Mobile = 'MOBILE',
  Tablet = 'TABLET',
  NarrowDesktop = 'NARROW',
  Desktop = 'DESKTOP',
}
```

and by our constant breakpoints:
```ts
export const BREAKPOINT_DESKTOP = 1300;
export const BREAKPOINT_TABLET = 1025;
export const BREAKPOINT_MOBILE = 680;
```
where window types are defined as follows:
```ts
const windowType: WindowTypes =
    width < BREAKPOINT_MOBILE
      ? WindowTypes.Mobile
      : width < BREAKPOINT_TABLET
      ? WindowTypes.Tablet
      : width < BREAKPOINT_DESKTOP
      ? WindowTypes.NarrowDesktop
      : WindowTypes.Desktop;
```

## 1. Use flex and relative dimensions

### Flex
The CSS `display: flex` property will be your best friend. A flex container alters its content's 
height, width, and/or position to fit the available space. I could explain it, but 
[this guide](https://css-tricks.com/snippets/css/a-guide-to-flexbox/) is pretty much all you'll 
need and something I 100% recommend always having on hand.

I recommend using flex over other layout solutions like AntD's 
[grid](https://ant.design/components/grid/) because it's more flexible and a lot simpler in the 
code, but be aware other solutions exist if flex isn't working for what you're working on.

### Relative Dimensions
Use standard dimensions like `px` whenever it looks reasonable to keep things simple, but relative 
dimensions can help you scale content to look appropriate for the screen. In particular, be aware 
that you can use:

| Unit   | Description                                                                  |
|--------|------------------------------------------------------------------------------|
| `vw`   | 1`vw` = 1% of the browser window's width                                     |
| `vh`   | 1`vh` = 1% of the browser window's height                                    |
| `vmin` | 1`vmin` = 1% of the browser window's smaller dimension (of width and height) |
| `vmax` | 1`vmax` = 1% of the browser window's larger dimension (of width and height)  |
| `%`    | Percentage of the parent element's respective dimension                      |

## 2. Rendering different content for different screen sizes: `useWindowDimensions`

To demonstrate how to render different content for different window types, let's build a component 
that renders what window type the user is on. We'll show a `h1` on desktop, `h2` on narrow desktop, 
`h3` on tablet, and `h4` on mobile.

First, we can get the user's window type with the `useWindowDimensions` 
[hook](https://reactjs.org/docs/hooks-overview.html), located under `src/components/windowDimensions`. 
To use the hook, simply add the following line to your component:

```ts
const { windowType } = useWindowDimensions();
```

!!! note

    The `useWindowDimensions` hook also gives us access to the window's exact width and height, but 
    in most cases the window type will be sufficient.
    ```ts
    const { width, height, windowType } = useWindowDimensions();
    ```

Second, we can change what to render in our React component with a switch statement:
```html
{(() => {
  switch (windowType) {
    case WindowTypes.Mobile:
      return <h4>You're on mobile!</h4>
    case WindowTypes.Tablet:
      return <h3>You're on tablet!</h3>
    case WindowTypes.NarrowDesktop:
      return <h2>You're on narrow desktop!</h2>
    case WindowTypes.Desktop:
      return <h1>You're on desktop!</h1>
  }
})()}
```

so our whole component would look something like:
```html
const WebPage: React.FC = () => {
  // get the window type
  const { windowType } = useWindowDimensions();
  
  return (
      <>
        {/* content that renders on all screens */}
        <h1>Welcome to the site that tells you what window type you're on!</h1>

        {/* content that renders based on window type */}
        {(() => {
          switch (windowType) {
            case WindowTypes.Mobile:
              return <h4>You're on mobile!</h4>
            case WindowTypes.Tablet:
              return <h3>You're on tablet!</h3>
            case WindowTypes.NarrowDesktop:
              return <h2>You're on narrow desktop!</h2>
            case WindowTypes.Desktop:
              return <h1>You're on desktop!</h1>
          }
        })()}
      </>
  );
}
```

!!! note

    Switch statements are just one way to *conditionally render* content in React based on `windowType`. 
    See [**here**](https://reactjs.org/docs/conditional-rendering.html) for other ways that will allow 
    you to do different things, such as only showing a component on desktop.

This was a pretty basic example, but in general we'll do this sort of responsive rendering to show 
different content or use different layouts for different window types. 

## 3. Changing the style of content based on the screen size: CSS `@media` queries

When we're just changing the *style* of components instead of what components are rendering, we 
use the CSS `@media` rule. In general, always try to do this over using `useWindowDimensions` 
(we'll explain why later).

To demonstrate, let's build a page that renders small text (`10px`) on mobile and medium text
(`16px`) on everything else.

First, let's build our page for everything except mobile.
```js
const StyledParagraph = styled.p`
  font-size: 16px;
`;

const WebPage: React.FC = () => {
  return (
      <>
        <StyledParagraph>Welcome to our amazing webpage!</StyledParagraph>
      </>
  );
}
```

!!! note

    We're using [`styled-components`](https://styled-components.com/) in this example and in our 
    project, but `@media` rules work whenever you're using CSS.

Now, to have our `StyledParagraph` be `10px` on mobile, we just have to add an `@media` rule in the 
CSS of our styled component:
```js
const StyledParagraph = styled.p`
  font-size: 16px;
  
  /* on any devices with a width less than BREAKPOINT_MOBILE... */
  @media (max-width: ${BREAKPOINT_MOBILE}px) {
    /* ...apply the following styles */
    font-size: 10px;
  }
`;

const WebPage: React.FC = () => {
  return (
      <>
        <StyledParagraph>Welcome to our amazing webpage!</StyledParagraph>
      </>
  );
}
```

And that's it! There's a lot more you can do with CSS media queries, which you can explore 
[**here**](https://www.w3schools.com/cssref/css3_pr_mediaquery.asp).

### Sidebar: We prefer to use CSS Media Queries over `useWindowDimensions`

It is possible to use our `useWindowDimensions` hook instead of CSS media queries to do what we 
just did above by using props in our styled component:
```js
interface StyledParagraphProps {
  readonly isMobile: boolean;
}

const StyledParagraph = styled.p`
  font-size: ${({ isMobile }: StyledParagraphProps) => (isMobile ? '10' : '16')}px;
`;

const WebPage: React.FC = () => {
  // get the window type
  const { windowType } = useWindowDimensions();
  const isMobile = windowType === WindowTypes.Mobile;
  
  return (
      <>
        <StyledParagraph isMobile={isMobile}>Welcome to our amazing webpage!</StyledParagraph>
      </>
  );
}
```

or 

```js
interface StyledParagraphProps {
  readonly fontSize: string;
}

const StyledParagraph = styled.p`
  font-size: ${({ fontSize }: StyledParagraphProps) => fontSize};
`;

const WebPage: React.FC = () => {
  // get the window type
  const { windowType } = useWindowDimensions();
  const fontSize = (windowType === WindowTypes.Mobile) ? '10px' : '16px';
  
  return (
      <>
        <StyledParagraph fontSize={fontSize}>Welcome to our amazing webpage!</StyledParagraph>
      </>
  );
}
```

etc. However, we prefer to use media queries because passing props around can get pretty messy. From 
the examples above, we can see that it's a lot more readable and quicker to use media queries. 
Further, following React best practices of having 
[smart and dumb components](https://medium.com/@dan_abramov/smart-and-dumb-components-7ca2f9a7c7d0), 
we only want to use the `useWindowDimensions` hook in our smart components (in our project, our 
containers). This means that we'd have to pass down a `windowType` or `isMobile` or similar prop to 
every component that we want to have it, which is pretty ridiculous if a component four levels down 
is the only one that needs it. Just to drive home the point:

```
// ComponentFour.tsx

interface StyledParagraphProps {
  readonly isMobile: boolean;
}

const StyledParagraph = styled.p`
  font-size: ${({ isMobile }: StyledParagraphProps) => (isMobile ? '10' : '16')}px;
`;

const ComponentFour: React.FC<StyledParagraphProps> = ({ isMobile }) => {
  return (
      <StyledParagraph isMobile={isMobile}>The thing that needs to be styled</StyledParagraph>
  );
}

// ComponentThree.tsx

interface ComponentThreeProps {
  readonly isMobile: boolean;
}

const ComponentThree: React.FC<ComponentThreeProps> = ({ isMobile }) => {
  return (
      <>
        <p>Component 3 content</p>
        <ComponentFour isMobile={isMobile} />
      </>
  );
}

// ComponentTwo.tsx

interface ComponentTwoProps {
  readonly isMobile: boolean;
}

const ComponentTwo: React.FC<ComponentTwoProps> = ({ isMobile }) => {
  return (
      <>
        <p>Component 2 content</p>
        <ComponentThree isMobile={isMobile} />
      </>
  );
}

// ComponentOne.tsx

interface ComponentOneProps {
  readonly isMobile: boolean;
}

const ComponentOne: React.FC<ComponentOneProps> = ({ isMobile }) => {
  return (
      <>
        <p>Component 1 content</p>
        <ComponentTwo isMobile={isMobile} />
      </>
  );
}

// WebPage.tsx

const WebPage: React.FC = () => {
  // get the window type
  const { windowType } = useWindowDimensions();
  const isMobile = windowType === WindowTypes.Mobile;
  
  return (
      <>
        <h1>Welcome to our amazing webpage!</h1>
        <ComponentOne isMobile={isMobile} />
      </>
  );
}
```

versus:

```
// ComponentFour.tsx

const StyledParagraph = styled.p`
  font-size: 16px;
  
  @media (max-width: ${BREAKPOINT_MOBILE}px) 
    font-size: 10px;
  }
`;

const ComponentFour: React.FC = () => {
  return (
      <StyledParagraph>The thing that needs to be styled</StyledParagraph>
  );
}

// ComponentThree.tsx

const ComponentThree: React.FC = () => {
  return (
      <>
        <p>Component 3 content</p>
        <ComponentFour />
      </>
  );
}

// ComponentTwo.tsx

const ComponentTwo: React.FC = () => {
  return (
      <>
        <p>Component 2 content</p>
        <ComponentThree />
      </>
  );
}

// ComponentOne.tsx

const ComponentOne: React.FC = () => {
  return (
      <>
        <p>Component 1 content</p>
        <ComponentTwo />
      </>
  );
}

// WebPage.tsx

const WebPage: React.FC = () => {
  return (
      <>
        <h1>Welcome to our amazing webpage!</h1>
        <ComponentOne />
      </>
  );
}
```

which leaves space for the props that matter.

## A more complicated example: Login Page

So far our examples have been pretty basic. Our login container shows a more interesting way of 
using the techniques we've described so far. (You can play with resizing the page 
[**here**](https://map.treeboston.org/login)).

On desktops, we show our standard design:

<img src="https://lh3.googleusercontent.com/c9IAgy0uqgvkMRDvlMdqYBXR15sFe1l-Dxnl3JnRffjGX8Ug8FlvDfzyZ8V1xNnlkV3qj2Oi3xp70PqRtFLZ6FOR3SqTsWVfA0SOiIvaYf8KRvRi1dYHk3kA9norroKI6e-z0ZsqSQ=w2400" alt="Login_Desktop" />

We have less whitespace on narrow desktop:

<img src="https://lh3.googleusercontent.com/Mf9v80ndK7ivczmHpez7kGj5PHql9F-8PlK6aXCbvFlC6TivJqPhWRUYzZslpXIYhXj5wwVSMPIa3n642rLTeAQHS21zl-LbWZre5mbUwDmj4igDUHAN7gT0r4oxJiJQUnDX43Ha8A" alt="Login_Narrow" />

The blocks stack on tablet:

<img src="https://lh3.googleusercontent.com/zfo8Dt6OqkxucRHhN-IaxOPWRe2g37Ime6ghpxwGFNyYK7xYKrAbtD3GH3Tv_se52NO9Ejff8TNOAXcJnbptU22HaaBvWaXQ1AEq2IHx5gZommuxZi3vhNy4BsTqnDR0WZYAJXG_-A" alt="Login_Tablet" />

And we have a much simpler design for mobile:

<img src="https://lh3.googleusercontent.com/Eghw1soJ-adU3G-g_EhfmyW_q6-QYqQrw_WZUqFdYP66rjQ7TG11M15kRMDvvmPF90V-mB_eGLPy6Kjl7guOqMxF1rwDY_icvM82IDSnVxgk43up2z69V2NKBpTF4YoEHlsP4WZA8A" alt="Login_Mobile" />

Let's look at the code to see how it works (narrowing in on the code that matters for styling). 
First, for `WindowTypes.Desktop`, `WindowTypes.NarrowDesktop`, and `WindowTypes.Tablet`, we 
render the same blocks and have mostly the same styling, but we use `flex` to make the grey and 
green boxes appear reasonably on the screen. We also use relative dimensions:

```js
const InputGreetingContainer = styled.div`
  width: 100vw; /* use relative dimensions to make the boxes as wide as the screen */
  
  /* make the boxes as tall as the parent container (the screen minus the nav bar), or 575px 
  (whichever is taller). this way, if the screen is short, we can scroll down to see the rest 
  of the content instead of making the content look too short. */
  min-height: 575px;
  height: 100%;
  
  display: flex; /* use flex to responsively position the boxes */
  
  /* by using wrap, the green box will be below the grey on tablet, 
  ensuring the boxes never get too skinny */
  flex-wrap: wrap;
  
  /* center the boxes in the screen and parent container */
  align-items: center;
  justify-content: space-around;
  align-content: center;
  
  /* define the gap between the boxes */
  gap: 20px;
`;

const Login: React.FC = () => {
  return (
      <InputGreetingContainer>
        {/* grey box */}
        <InputContainer>
          <Title>{LOGIN_TITLE}</Title>
          <Line />
          <LoginForm
              formInstance={loginForm}
              onFinish={onFinish}
              windowType={windowType}
          />
          {ForgotPasswordFooter}
        </InputContainer>

        {/* green box */}
        <GreetingContainer
            header={LOGIN_HEADER}
            body={LOGIN_BODY}
        />
      </InputGreetingContainer>
  );
}
```

We also add a media rule to `InputGreetingContainer` to make sure the boxes are big enough on 
tablets (since the paragraphs are not as wide, they'll need to have more lines, making them taller 
than on desktops).

```js
export const InputGreetingContainer = styled.div`
  width: 100vw;
  min-height: 575px;
  height: 100%;
  display: flex;
  flex-wrap: wrap;
  align-items: center;
  justify-content: space-around;
  align-content: center;
  gap: 20px;

  /* make sure the boxes are tall enough to fit all content on tablets */
  @media (max-width: ${BREAKPOINT_TABLET}px) {
    min-height: 900px;
  }
`;
```

Finally, for mobile we'll use `useWindowDimensions` to not render the boxes at all:

```js
export const InputGreetingContainer = styled.div`
  width: 100vw;
  min-height: 575px;
  height: 100%;
  display: flex;
  flex-wrap: wrap;
  align-items: center;
  justify-content: space-around;
  align-content: center;
  gap: 20px;

  /* make sure the boxes are tall enough to fit all content on tablets */
  @media (max-width: ${BREAKPOINT_TABLET}px) {
    min-height: 900px;
  }
`;

const Login: React.FC = () => {
  const { windowType } = useWindowDimensions();
  
  return (
      <>
        {(() => {
          switch (windowType) {
            case WindowTypes.Mobile:
              return (
                  <MobileLoginPageContainer>
                    <PageHeader pageTitle={LOGIN_TITLE} isMobile={true} />
                    <LoginForm
                        formInstance={loginForm}
                        onFinish={onFinish}
                        windowType={windowType}
                    />
                    {ForgotPasswordFooter}
                  </MobileLoginPageContainer>
              );
            case WindowTypes.Tablet:
            case WindowTypes.NarrowDesktop:
            case WindowTypes.Desktop:
              return (
                  <PageLayout>
                    <InputGreetingContainer>
                      <InputContainer>
                        <Title>{LOGIN_TITLE}</Title>
                        <Line />
                        <LoginForm
                            formInstance={loginForm}
                            onFinish={onFinish}
                            windowType={windowType}
                        />
                        {ForgotPasswordFooter}
                      </InputContainer>

                      <GreetingContainer
                          header={LOGIN_HEADER}
                          body={LOGIN_BODY}
                      />
                    </InputGreetingContainer>
                  </PageLayout>
              );
          }
        })()}
      </>
  );
};
```

Congrats! You've made it to the end :tada:
