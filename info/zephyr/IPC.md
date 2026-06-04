# Zephyr Interprocess Communication
Pretty much all IPC in Zephyr is done through message queues, so this document will just focus on those.

## Message Queue Basics
[Zephyr Message Queue Documentation](https://docs.zephyrproject.org/latest/kernel/services/data_passing/message_queues.html)

A message queue is a [ring buffer](https://en.wikipedia.org/wiki/Circular_buffer) in Kernel memory. It has the following properties:
- A data item size in bytes
- A maximum quantity of data items that it can hold (so the Kernel knows how much memory to use for it)

### Definition
```c
struct data_item_type {
    uint32_t field1;
    uint32_t field2;
    uint32_t field3;
};

char my_msgq_buffer[10 * sizeof(struct data_item_type)];
struct k_msgq my_msgq;

k_msgq_init(&my_msgq, my_msgq_buffer, sizeof(struct data_item_type), 10);
```

Alternatively, it is easier (and faster, since macros are compile-time) to use the `K_MSGQ_DEFINE` macro:
```c
K_MSGQ_DEFINE(my_msgq, sizeof(struct data_item_type), 10, 1);
```

This does both the buffer and message queue initialization steps from above, meaning all you need to do is make the struct and call this.

## Interaction with a MSQ
- `k_msgq_put` copies a piece of data into the queue
- `k_msgq_get` moves a piece of data into the calling threads memory
- `k_msgq_peek` looks at a piece of data without moving it
- `k_msgq_num_free_get` gets the amount of free spaces available in a MSQ

