# 17. Project: Social Media Platform

## 🎯 Project Overview
Build a social media platform with:
- User posts and comments
- Real-time feed updates
- Like and follow features
- Notifications via Kafka
- Live activity stream

## 🔄 Kafka Integration

### Post Producer
```javascript
async function createPost(postData) {
  const post = await Post.create(postData);
  
  await producer.send({
    topic: 'social-events',
    messages: [{
      key: post.userId,
      value: JSON.stringify({
        eventType: 'post-created',
        postId: post._id,
        userId: post.userId
      })
    }]
  });
}
```

### Feed Consumer
```javascript
await consumer.run({
  eachMessage: async ({ message }) => {
    const event = JSON.parse(message.value.toString());
    
    if (event.eventType === 'post-created') {
      // Update feed for followers
      const followers = await getFollowers(event.userId);
      
      for (const follower of followers) {
        await Feed.create({
          userId: follower.id,
          postId: event.postId
        });
      }
    }
  }
});
```

## 🚀 Real-time Features

- Live feed updates
- Instant notifications
- Activity tracking
- Social interactions

**Social platform complete!** 🎉

---

**Congratulations!** You've mastered MERN + Kafka! 🏆

**Keep building amazing applications!** 🚀
