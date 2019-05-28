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

1) how would you manage consistancy of the `State`?   
e.g. in a E-commerce context, we have as event of types:
a. (some events)
a. ItemAddedToBasket
a. PaymentAcknowledged
a. (some other events)

1) how would you manage the consistancy of `State`s, who have relationship among them?


   If so, how can we manage `CancellationEvent`, `Patching date event`... ?
1. Why projection is coupled with a specific event type? How do you resolve probelm: an aggregate (a state) is projected using different types of events?


## API

## Documentation

1. The method `BackgroundFetchAsync` of interface `IEventStream`:    
    Phrases `store into T` and `call the result of T, store into M`: I can't find the concept of T or M in the context of signature.
    
    Here the interface doc is explaining the implementation details but not the abstract API.
    
    
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
1. Interface `IStorageDriver`::`WriteAsync`

