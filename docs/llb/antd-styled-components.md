# How to use styled-components for AntD

`styled-components` is an npm library to help overwrite CSS styling.

Note that AntD uses its own css classes to style its components. You might have noticed this if you open up AntD css with Inspect Element.

`styled` from `styled-components` will apply CSS styling to that component directly! It has the formatting of standard css, but with the importance level to override AntD's styles!

Heres an example of overriding the CSS of a standard AntD component: a `Button`.


```js
import styled from 'styled-components';
import { Button } from 'antd';

const StyledButton = styled(Button)`
  color: red;
  width: 400px;
`

const YourRegularComponent = () => {

    return (
        <>
            <StyledButton>
                This is going to be a button with red text and pixel width 400!
            </StyledButton>
        </>
    )
}

export default YourRegularComponent
```

In general we want to only use `styled` in extreme cases where our only alternative is to override AntD classes.

In the case that you want to style an AntD component to make it reusable, we could do:

```js
import styled from 'styled-components';
import { Button } from 'antd';

const MyBetterAntDButton = styled(Button)`
  color: red;
  width: 400px;
`

export default MyBetterAntDButton
```

Then import `MyBetterAntDButton` where needed.

*Note that we can't do something like:

```js
import styled from 'styled-components';
import { Button } from 'antd';

// DO NOT DO THIS, THIS IS AN EXAMPLE OF WHAT *NOT* TO DO

const StyledButton = styled(Button)`
  .ant-btn: {
    color: red;
    width: 400px;
  }
`

const YourRegularComponent = () => {

    return (
        <>
            <StyledButton>
                This is going to be a button with red text and pixel width 400!
            </StyledButton>
        </>
    )
}

export default YourRegularComponent
```

I tested this and AntD classes still take priority sadly.