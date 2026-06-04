# Zephyr Thread Model
## Registration
A Zephyr thread can be created with a line like the following:

```c
// Statically define and initialize the thread
K_THREAD_DEFINE(interval_tid, STACK_SIZE, interval_thread_entry, NULL, NULL, NULL,
                THREAD_PRIORITY, 0, 0);
```

A thread itself needs a while loop, and for our purposes it executes its task within that loop and then sleeps until woken by interval or event.

## Interval Wake
```c
void interval_thread_entry(void) {
    while (1) {
        // Task logic here

        // Yield the CPU for a specific interval
        k_msleep(INTERVAL_TIME_MS);
    }
}

// Statically define and initialize the thread
K_THREAD_DEFINE(interval_tid, STACK_SIZE, interval_thread_entry, NULL, NULL, NULL,
                THREAD_PRIORITY, 0, 0);
```

## Event Wake
```c
// Define a message queue for incoming data
K_MSGQ_DEFINE(rx_msgq, sizeof(data_packet_t), 10, 4);

void event_thread_entry(void) {
    data_packet_t rx_data;

    while (1) {
        // Blocks and yields CPU until a message is placed in the queue
        if (k_msgq_get(&rx_msgq, &rx_data, K_FOREVER) == 0) {
            // Task logic here
        }
    }
}
```

## Mixed Wake
```c
#define INTERVAL_TIMEOUT K_MSEC(5000) 

void communications_thread_entry(void) {
    data_packet_t rx_data;

    while (1) {
        // Block until a message arrives OR the interval elapses
        int ret = k_msgq_get(&rx_msgq, &rx_data, INTERVAL_TIMEOUT);

        if (ret == 0) {
            // WOKEN BY EVENT: Message received
            // Thread Logic
        } else if (ret == -EAGAIN) {
            // WOKEN BY INTERVAL: Timeout expired
            // Thread Logic
        }
    }
}
```

## Thread Control
With our system of setting up System Health as the thread manager, we have to have a bit of a unique setup.

For the child threads that start only on conditions, we define its thread with K_FOREVER:
```c
K_THREAD_DEFINE(payload_tid, STACK_SIZE, payload_thread_entry, NULL, NULL, NULL,
                PRIORITY, 0, K_FOREVER);
```

Then the System Health needs the thread IDs (in something like `thread_defs.h`):
```c
extern const k_tid_t payload_tid;
extern const k_tid_t comms_tid;
```

Then system health can start a thread like this:
```c
if (DEPLOY_COMPLETE && (OP_STATUS == NOMINAL || OP_STATUS == HIGH)) {
    // Only starts the thread if it isn't already running
    k_thread_start(payload_tid); 
}
```
