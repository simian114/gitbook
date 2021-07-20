---
description: 별거 없다
---

# Integration testing하기

> addInput 에 입력을 하고..... 버튼 이벤트를 발생시키면 `TodoList` 컴포넌트에 추가한 `Todo` 가 생기는지를 테스트하고싶다. 즉, 하나의 컴포넌트만이 아니라 해당 컴포넌트의 이벤트로 인해 다른 컴포넌트와 상호작용하는걸 테스트

아주 간단하다. `fireEvent` 를 통해 이벤트를 발동한다음 `get, ..` 등의 셀렉터를 통해 특정 요소를 잡아오면 된다! \(특정 컴포넌트만을 `render` 해도 `getBy..` 등의 셀렉터로 다른 컴포넌트의 `html` 을 잡아올 수 있음\)

```javascript
const MockTodo = ({ numberOfIncompleteTasks }) => (
  <BrowserRouter>
    <Todo />
  </BrowserRouter>
);

const addTasks = (tasks) => {
  const inputElement = screen.getByPlaceholderText(/Add a new task here.../i);
  const buttonElement = screen.getByRole('button', { name: /Add/i });
  tasks.forEach((task) => {
    fireEvent.change(inputElement, { target: { value: task } });
    fireEvent.click(buttonElement);
  });
};

describe('Todo', () => {
  it('tasks should not have completed class when intially rendered', async () => {
    render(<MockTodo />);
    const tasks = ['test1'];
    addTasks(tasks);

    const divElement = screen.getByText('test1');
    expect(divElement).not.toHaveClass('todo-item-active');
  });

  it('tasks should have completed class when clicked', async () => {
    render(<MockTodo />);
    const tasks = ['test1'];
    addTasks(tasks);

    const divElement = screen.getByText('test1');
    fireEvent.click(divElement);
    expect(divElement).toHaveClass('todo-item-active');
  });
});
```



