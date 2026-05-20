# Custom FreeRTOS Notes
## Design
- Minimalist, doesn't provide much.
- Tasks with context switching
- Queues

## Goals
### Power Budget Requirements
From [the website](https://freertos.org/Why-FreeRTOS/FAQs/Memory-usage-boot-times-context#how-much-ram-does-freertos-use), FreeRTOS needs:
- Scheduler: 236 bytes
- Each queue:
  - 76 bytes for setup
  - x bytes for queue space
- Each task:
  - 64 bytes for setup
  - Task stack size

So we could get away with a pretty minimal memory footprint. With our code, we'd probably need at least 10kb

### Testability
It has nothing built in, but you can run it in POSIX mode, which (from the way I understand it), runs separate threads under a POSIX process. With that, you can use #ifdef blocks to open sockets to mock devices, and have SITL testing that way.

Additionally, since most everything is functions, it isn't too difficult to unit test.

Running as POSIX threads also makes it easy to run things like valgrind, which would be incredibly useful.

### High Modularity
There is little to no enforced modularity. You have separate tasks to be able to context switch between things, so it beats out bare-metal in that regard, but there's not a whole lot enforced for inputs and outputs like in F'. Message queues are also pretty minimal, and are general `void *` casts.

Theoretically possible, but comes down a lot to explicit good software design

### Lower Learning Curve
Possibly the best for this. If a student has taken anything in systems they can be taught RTOS basics in under a day. Hell, the code for it is right there so you could just read the whole thing if you wanted to.

However, though it wouldn't be nearly as much upfront, there are a lot of aspect we'd have to make ourselves, and thus it opens the door for technical debt.

## Conclusion
This is a very promising option, but it shifts most problems from Software Architecture to Software Design, and we would need to be diligent with maintaining high quality code throughout
