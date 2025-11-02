# 13. Kafka Streams - Stream Processing

## 🎯 Learning Objectives
- Understand Kafka Streams
- Process data streams
- Implement transformations
- Windowed operations
- Real-time aggregations

## 🔄 Stream Processing

Stream processing analyzes data in real-time.

### Basic Stream
```javascript
const { Kafka } = require('kafkajs');

const kafka = new Kafka({ brokers: ['localhost:9092'] });
const producer = kafka.producer();
const consumer = kafka.consumer({ groupId: 'stream-processor' });

// Process stream
await consumer.run({
  eachMessage: async ({ message }) => {
    const data = JSON.parse(message.value.toString());
    const processed = transformData(data);
    
    await producer.send({
      topic: 'processed-events',
      messages: [{ value: JSON.stringify(processed) }]
    });
  }
});
```

## 🔗 Next Steps

Stream processing complete! Let's build projects!

---

**Key Takeaways:**
- Stream processing in real-time
- Transform data as it flows
- Window operations for aggregations
- Process and forward to new topics
- Handle backpressure

**Next Tutorial:** [15-Project-Real-time-Dashboard.md](./15-Project-Real-time-Dashboard.md)
