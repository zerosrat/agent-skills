---
title: Use ahooks useRequest for Request Management
impact: MEDIUM-HIGH
impactDescription: automatic deduplication
tags: client, ahooks, deduplication, data-fetching
---

## Use ahooks useRequest for Request Management

ahooks useRequest provides automatic request management, caching, deduplication, and polling capabilities.

**Incorrect (manual state management, no caching):**

```tsx
function UserList() {
  const [users, setUsers] = useState([])
  const [loading, setLoading] = useState(false)
  useEffect(() => {
    setLoading(true)
    fetch('/api/users')
      .then(r => r.json())
      .then(setUsers)
      .finally(() => setLoading(false))
  }, [])
}
```

**Correct (automatic loading states and error handling):**

```tsx
import { useRequest } from 'ahooks'

function UserList() {
  const { data: users, loading, error } = useRequest(() => 
    fetch('/api/users').then(r => r.json())
  )
}
```

**For mutations (optimistic updates):**

```tsx
import { useRequest } from 'ahooks'

function UserList() {
  const { data: users, mutate } = useRequest(
    () => fetch('/api/users').then(r => r.json())
  )
  
  const handleDelete = async (userId: string) => {
    // Optimistic update: immediately update UI
    mutate(users.filter(u => u.id !== userId))
    
    try {
      await fetch(`/api/users/${userId}`, { method: 'DELETE' })
      // Optionally refetch to ensure sync
      // refresh()
    } catch (error) {
      // Revert on error
      mutate(users)
    }
  }
  
  return (
    <div>
      {users?.map(user => (
        <div key={user.id}>
          {user.name}
          <button onClick={() => handleDelete(user.id)}>Delete</button>
        </div>
      ))}
    </div>
  )
}
```

Reference: [https://ahooks.js.org/hooks/use-request](https://ahooks.js.org/hooks/use-request)
