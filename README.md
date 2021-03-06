Queue
===========

A NIO block for queueing up signals. As signals pile up, the Queue block releases a configurable number at a configurable interval. If incoming signals would overflow the queue, signals are popped off the front as needed. These overflow signals are simply dropped, not notified and sent to the next block.

If a *group_by* string is configured, incoming signals are divided and grouped by the value of that attribute. The configured capacity applies to *each* such queue, not the block as a whole.

If a *uniqueness* expression is set, then each queue will only contain signal that are unique according to the expression. If a signl is to be appended to the queue that matches a signal already in the queue, then it will be dropped.

A negative *interval* mean that signals will not be emitted at any interval. Instead, the *emit* command is the only way for the block to notify signals.

Uses persistance to maintain queues between stopping and starting of the block.

Properties
--------------

-   **capacity**: Size of each queue. When queue exceeds capacity, a signal is popped off the end and *not* notified.
-   **group_by**: Signal attribute that determines what queue to put the signal in. Defaults to group 'null' if attribute does not exist or *group_by* is unspecified.
-   **chunk_size**: Number of signals to notify each *interval* period and *emit* command.
-   **interval**: Period at which signals are notified from queues.
-   **backup_interval**: Period at which queues are backed up to disk using persistance. Queues are also backed up on stop.
-   **reload** (default=False): If True, notified signals immediately get reloaded back onto the end of the queue they came off of.
-   **uniqueness**: Expression Property. If specified, each queue (i.e. *group_by*) will not allow multiple signals that evaluate to the same *uniqueness*. If a signal comes in that matches *group_by* and *uniqueness* with a signal already in the queue, then it the new signal dropped.

Dependencies
----------------
None

Commands
----------------

-   **emit**: Notify signals off the end of the queues.
-   **remove** (query="", group=""): Remove signals from the queue *group* where *query* evaluates to True. *query* uses the Expression Property syntax for evaluatuation. For example, to remove all signals, use `{{True}}` or to remove signals with *val* equal to 1 use `{{$val == 1}}`. If no *group* is specified, then all groups are inspected. Signals are not notified.
-   **view** (query="", group=""): View signals in the queue *group*. If *group* is not specified, all signals in all queues are returned. Signals are not notified.
-   **update_props** (props=""): Modify the value of a property on a started block. Parameter *props* is a dictionary where the *key* is a block property and the *value* is the new value. *interval* is the only property currently supported. When *interval* is updated, the recurring emit job will be cancelled and restarted, even if the new value is equal to the previous one.

Input
-------
Any list of signals. If *group_by* is uses, then that attribute is expected to be on the incoming signals. Otherweise signals go to default 'null' group.

Output
---------
List of signals emitted from queues each *interval* period and *emit* command.
