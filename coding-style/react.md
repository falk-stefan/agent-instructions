# React Coding Style

## State Management for Components

Complex components should follow the model-view-update pattern and implement a reducer function for state management.

Use this pattern when a state has multiple related transitions or when the next state depends on the current state.

```typescript
type State =
  | { status: 'loading' }
  | { status: 'error'; message: string }
  | { status: 'ready'; data: Item[] };

type Action =
  | { type: 'refresh' }
  | { type: 'loaded'; data: Item[] }
  | { type: 'error'; message: string }
  | { type: 'update'; item: Item }
  | { type: 'remove'; id: number };

export const reducer = (current: State, action: Action) => {
  switch (action.type) {
    case 'refresh':
      return {status: 'loading'};

    case 'loaded':
      return {status: 'ready', data: action.data};

    case 'error':
      return {status: 'error', message: action.message};

    case 'update':
      if (state.status !== 'ready') return state;

      return {
        status: 'ready',
        data: state.data.map(item =>
          item.id === action.item.id ? action.item : item
        ),
      };

    case 'remove':
      if (state.status !== 'ready') return state;

      return {
        status: 'ready',
        data: state.data.filter(item => item.id !== action.id),
      };
  }
}
```
