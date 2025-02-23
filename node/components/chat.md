This would work as a form as well, where only the first component will grow the rest of the space

```tsx
<div className='flex space-x-2'>
    <div className='flex-grow'>
        <Input />
    </div>
    <div>
        <Button>Button</ Button>
    </div>
<div>
```

This would also apply for the whole chat window

```tsx
<div className="flex flex-col space-y-2">
  <ScrollArea className="flex-grow p-4">
    {/** loop through the messages here */}
  </ScrollArea>
  <form className="flex space-x-2">
    <div className="flex-grow">
      <Input />
    </div>
    <div>
      <Button>Button</Button>
    </div>
  </form>
</div>
```

Here's the styling for the chat bubble

```tsx
export const ChatBubble: React.FC<ChatBubbleProps> = ({ message, isUser }) => {
  return (
    <div className={`flex ${isUser ? "justify-end" : "justify-start"} mb-4`}>
      <div
        className={`flex items-end ${isUser ? "flex-row-reverse" : "flex-row"}`}
      >
        <div
          className={`flex items-center justify-center w-8 h-8 rounded-full ${
            isUser ? "bg-blue-500" : "bg-green-500"
          } text-white`}
        >
          {isUser ? <User size={16} /> : <Bot size={16} />}
        </div>
        <div
          className={`max-w-xs mx-2 px-4 py-2 rounded-lg ${
            isUser
              ? "bg-blue-500 text-white dark:bg-blue-600"
              : "bg-green-500 text-white dark:bg-green-600"
          }`}
        >
          <p className="text-sm">{message}</p>
        </div>
      </div>
    </div>
  );
};
```
