# 16. Project: Event-Driven E-commerce

## 🎯 Project Overview
Build a scalable e-commerce platform with:
- Product catalog
- Shopping cart
- Order processing
- Inventory management
- Real-time updates via Kafka

## 🔄 Event Flow

```
User places order
    ↓
Express API publishes 'order-created'
    ↓
Kafka Consumers process:
    ├── Inventory Service (reduce stock)
    ├── Payment Service (process payment)
    ├── Shipping Service (create shipment)
    └── Notification Service (send emails)
```

## 🚀 Implementation

### Order Producer
```javascript
const orderProducer = kafka.producer();

async function createOrder(orderData) {
  const order = await Order.create(orderData);
  
  // Publish event
  await orderProducer.send({
    topic: 'order-events',
    messages: [{
      key: order.id,
      value: JSON.stringify({
        eventType: 'order-created',
        orderId: order.id,
        items: order.items,
        total: order.total
      })
    }]
  });
  
  return order;
}
```

### Inventory Consumer
```javascript
const inventoryConsumer = kafka.consumer({ groupId: 'inventory-service' });

await inventoryConsumer.run({
  eachMessage: async ({ message }) => {
    const event = JSON.parse(message.value.toString());
    
    if (event.eventType === 'order-created') {
      // Reduce inventory
      for (const item of event.items) {
        await Product.findByIdAndUpdate(
          item.productId,
          { $inc: { stock: -item.quantity } }
        );
      }
    }
  }
});
```

## 🎉 Complete Features

- ✅ Product browsing
- ✅ Shopping cart
- ✅ Order placement
- ✅ Event-driven processing
- ✅ Real-time updates

**E-commerce platform complete!**

---

**Next Tutorial:** [17-Project-Social-Media.md](./17-Project-Social-Media.md)
