# 09. Testing MERN Applications

## 🎯 Learning Objectives
- Write unit tests
- Implement integration tests
- Test React components
- Test Express APIs
- Test Kafka producers/consumers

## 🧪 Testing Setup

### Install Testing Tools
```bash
# Backend
npm install --save-dev jest supertest

# Frontend
npm install --save-dev @testing-library/react @testing-library/jest-dom
```

## ✅ Backend Testing

### Jest Configuration
```javascript
// package.json
{
  "scripts": {
    "test": "jest",
    "test:watch": "jest --watch"
  },
  "jest": {
    "testEnvironment": "node"
  }
}
```

### API Testing
```javascript
const request = require('supertest');
const app = require('../server');
const User = require('../models/User');

describe('User API', () => {
  beforeEach(async () => {
    await User.deleteMany();
  });
  
  test('should create user', async () => {
    const response = await request(app)
      .post('/api/users')
      .send({ name: 'Test', email: 'test@example.com' });
    
    expect(response.status).toBe(201);
    expect(response.body.name).toBe('Test');
  });
  
  test('should get all users', async () => {
    await User.create({ name: 'User 1', email: 'user1@example.com' });
    
    const response = await request(app)
      .get('/api/users');
    
    expect(response.status).toBe(200);
    expect(response.body.length).toBe(1);
  });
});
```

## ⚛️ React Testing

```javascript
import { render, screen, fireEvent } from '@testing-library/react';
import Counter from './Counter';

test('increments counter', () => {
  render(<Counter />);
  const button = screen.getByText('+');
  fireEvent.click(button);
  expect(screen.getByText('Count: 1')).toBeInTheDocument();
});
```

## 🔗 Next Steps

Testing complete! Let's move to Kafka!

---

**Key Takeaways:**
- Jest for backend testing
- Supertest for API testing
- React Testing Library for components
- Write tests before features
- Test edge cases and errors

**Next Tutorial:** [10-Kafka-Introduction.md](./10-Kafka-Introduction.md)
