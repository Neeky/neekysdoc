# aeProcessEvents

简单的来讲是从事件循环中取出 fd ，并调用之前就设置好的回调函数；简化后的如下

```c
int aeProcessEvents(aeEventLoop *eventLoop, int flags)
{
    int processed = 0, numevents;
    numevents = aeApiPoll(eventLoop, tvp);

    for (j = 0; j < numevents; j++) {
        int fd = eventLoop->fired[j].fd;
        aeFileEvent *fe = &eventLoop->events[fd];
        int mask = eventLoop->fired[j].mask;

        /* Note the "fe->mask & mask & ..." code: maybe an already
         * processed event removed an element that fired and we still
         * didn't processed, so we check if the event is still valid.
         *
         * Fire the readable event if the call sequence is not
         * inverted. */
        if (!invert && fe->mask & mask & AE_READABLE) {
            // 调用回调函数
            fe->rfileProc(eventLoop,fd,fe->clientData,mask);
            fired++;
            fe = &eventLoop->events[fd]; /* Refresh in case of resize. */
        }

        /* Fire the writable event. */
        if (fe->mask & mask & AE_WRITABLE) {
            if (!fired || fe->wfileProc != fe->rfileProc) {
                // 调用回调函数
                fe->wfileProc(eventLoop,fd,fe->clientData,mask);
                fired++;
            }
        }
        
        /* If we have to invert the call, fire the readable event now
         * after the writable one. */
        if (invert) {
            fe = &eventLoop->events[fd]; /* Refresh in case of resize. */
            if ((fe->mask & mask & AE_READABLE) &&
                (!fired || fe->wfileProc != fe->rfileProc))
            {
                // 调用回调函数
                fe->rfileProc(eventLoop,fd,fe->clientData,mask);
                fired++;
            }
        }
        processed++;
    }
}
```