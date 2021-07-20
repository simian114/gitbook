# event 발생시키기

{% embed url="https://testing-library.com/docs/guide-events/" caption="링크를 보면 잘 나와있음" %}

```javascript
it('should be abel to type in input', async () => {
  render(<AddInput todos={[]} setTodos={mockedSetTodo} />);
  const element = screen.getByPlaceholderText(/Add a new task here.../i);
  // NOTE: trigger event
  fireEvent.change(element, { target: { value: 'Go Grocery Shopping' } });
  expect(element.value).toBe('Go Grocery Shopping');
});
it('should have empty input when add button is clicked', async () => {
  render(<AddInput todos={[]} setTodos={mockedSetTodo} />);
  const InputElement = screen.getByPlaceholderText(/Add a new task here.../i);
  const buttonElement = screen.getByRole('button', { name: /Add/ });
  // NOTE: trigger event
  fireEvent.change(InputElement, {
    target: { value: 'Go Grocery Shopping' },
  });
  fireEvent.click(buttonElement);
  expect(InputElement.value).toBe('');
});
```

코드를 보면 `input` 엘리먼트를 찾은 다음 `fireEvent` 로 값을 넣고, 그리고 버튼을 누르는 동작을 하고 있다.

