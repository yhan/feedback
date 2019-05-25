# Points of interest

1. The result of projection is immutable
   Concurrency is not managed at this level.
   
   
# Question

## Projection
1. We can apply an event sequence number 42 to a known state with last event sequence number 1?
   If so, how can we manage `CancellationEvent`, `Patching date event`... ?
   
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

