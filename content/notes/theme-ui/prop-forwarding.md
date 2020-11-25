# Prop Forwarding

I wanted to do this:

```
<Text as="time" datetime={post.date.datetime} sx={{ fontSize: 1 }}>
  {post.date.formatted}
</Text>
```

but it turned out that the `datetime` prop does not get forwarded to `time`. By
default,
[Emotion forwards all props](https://emotion.sh/docs/styled#customizing-prop-forwarding),
but allows customization of the forwarding function.

Every Theme UI component ends up using
[`Box`](https://github.com/system-ui/theme-ui/blob/master/packages/components/src/Box.js),
which implements a custom forwarding function that forwards only `space` and
`color` props.

This means that we need to use the custum pragma to add `sx` prop to native HTML
elements.