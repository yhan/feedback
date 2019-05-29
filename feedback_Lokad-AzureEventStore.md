fead back of repo: https://github.com/Lokad/AzureEventStore

# General


# Points of interest

## Read projection
1. The result of projection is immutable.  
   Concurrency is not managed at this level.
1. An event can be projected with different ways(using different type of projections) to differnt type of `State`
   
## Underlying storage
1. For retriving events, we provide the expected starting position to write, but also interestly with constraint of size of read events. By which we can support chunked read, avoiding GC gen_0 pressure.

    > Remark/question: the semantic optimistic lock is *by convention* providing `lastKnownEventSequenceNumber`. In this lib, we have two notion: a. StartWritingPosition and lastKnownPosition.
    
    won't it be more simple to use only just `lastKnownPosition`. Then
    
    
    ```csharp
    // lastKnownPosition from the actor of write operation
    // and _lastKnownPosition from the cache
    
     if(lastKnownPosition != _lastKnownPosition)
     {
        await RefreshCache(cancel);
     }
    ```
    instead of:
    
    ```csharp
     if(position >= _lastKnownPosition)
     {
        await RefreshCache(cancel);
     }
    ```
1. blafdqmfqm

# Question

## Projection
1. We can apply an event sequence number 42 to a known state with last event sequence number 1? In this case, 

    1. how would you manage consistency of the `State`?   
    e.g. in a E-commerce context, we have as event of types:  
        1. (some events)  
        1. ItemAddedToBasket  
        1. PaymentAcknowledged
        1. (some other events)

    1. how would you manage the consistency of `State`s, who have relationship among them?

    1. how can we manage `CancellationEvent`, `Patching date event`... ?


1. Why projection is coupled with a specific event type? How do you resolve problem: an aggregate (a state) is projected using different types of events?


## API

## Documentation

1. The method `BackgroundFetchAsync` of interface `IEventStream`:    The remarks is describing the implementation but not interface itself.
    
    ```csharp
    /// <summary> Almost thread-safe version of <see cref="Extensions.FetchAsync"/>. </summary>
    /// <remarks>
    /// You may call <see cref="IEventStream{TEvent}.TryGetNext"/> while the task is running, but NOT
    /// any other method. The function returned by the task is not thread-safe.
    ///
    /// This is intended for use in a "fetch in thread A, process in thread B"
    /// pattern:
    ///  - call BackgroundFetchAsync, store into T
    ///  - repeatedy call TryGetNext until either T is done or no more events
    ///  - call the result of T, store into M
    ///  - if M is false or the last call to TryGetNext returned an event, repeat
    /// </remarks>

    Task<Func<bool>> BackgroundFetchAsync(CancellationToken cancel = default(CancellationToken));
    ```

    Phrases `store into T` and `call the result of T, store into M`: I can't find the concept of T or M in the context of signature.    
    Here the interface doc is explaining the implementation details but not the abstract API. I would push the remarks to concrete implementation.
    
1. Ambiguous wording in comment:

what is `the object`?

```csharp
    internal interface IReifiedProjection
    {
        /// <summary> Attempt to save this projection to the destination stream. </summary>
        /// <remarks>
        /// The returned task should not access the object in any way, so the object
        /// may be safely accessed before the task has finished executing.
        /// </remarks>
        Task TrySaveAsync(CancellationToken cancel = default(CancellationToken));
```
1. Inconsistent documentation style:
Sometimes, implementation doc ref interface doc, sometimes it duplicate the one of interface. e.g
`
- ref: `EventStream<TEvent>::DiscardUpTo`
- duplicate: `ReifiedProjection`::`TrySaveAsync`
