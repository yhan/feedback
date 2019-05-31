*fead back of Lokad AzureEventStore repository: https://github.com/Lokad/AzureEventStore*

# Disclaimer
This feedback is based on my limited comprehension on the library. I put down my understanding, some questions are embedded inline.


# General

# Why this library?
Instead of using out-of-box EventStore. For POC?

# Concept
In addition to README.md of git repository, some points that I consider important to keep in mind:

1. EventStreamService, do:  
   + write stream   
   + read stream and construct internal `State`
     - the way of construction depends on user of this library
   + Threading
     - Enqueue of CatchUp in a dedicated thread
     - CatchUp and Writing to stream in another dedicated thread

1. Each time I want to create a new stream, I should create new instance of `EventStreamService<TEvent, TState>`


# Points of interest

## Read projection
1. The result of projection is immutable. Thus no worry for concurrent access.
1. An event can be projected with different ways (using different types of projections) to different type of `State`
   
## Underlying storage
1. Chunked read
    
   For retrieving events, we provide the expected starting position to write, but also with constraint of size of read events. By which we can support chunked read, for optimizing network traffic and have reasonable round trip delay.

# Question

## Event sequence

Apparently events can be **merged** as per:
```csharp
 /// <summary> Raw event data, as read and written through a <see cref="IStorageDriver"/>. </summary>
    internal sealed class RawEvent
    {
        /// <summary>
        /// The sequence number of the event: these should be strictly increasing, 
        /// but not necessarily contiguous (e.g. in case of stream rewrites, events may be merged).
        /// </summary>
        public readonly uint Sequence;

    }
```
Can I get more info for `rewrite` and `merge`? My doubt is: how can we maintain domain event concept if events got merged?

## Projection
1. We can apply any event with a sequence number greater than the one of the last event processed by this projection. Why we don't impose constraint applyingEvent.Sequence should exactly be lastAppliedEvent.Sequence + 1 ?   

   Without the above constraint,

    -  how would you manage consistency of the `State`?   
    e.g. in a E-commerce context, we have as event of types:  
        1. (some events)  
        1. ItemAddedToBasket  
        1. PaymentAcknowledged
        1. (some other events)

    - how would you manage the consistency of `State`s, who have relationship among them?

    - how can we manage `CancellationEvent`, `Patching date event`... ?


## API

## Implementation details
1. Why `unit` type is chosen for `Sequence` instead of `long`?

1. Readability of API:

what's going on here in `EventStreamWrapper<TEvent, TState>`?

```csharp
    /// <summary> Append events, constructed from the state, to the stream.
    /// </summary>
    /// <remarks> 
    /// Builder returns array of events to be appended, and additional data
    /// that will be returned by this method. Builder may be called more than
    /// once. 
    /// </remarks>
public async Task<AppendResult<T>> AppendEventsAsync<T>(
    Func<TState, Append<TEvent, T>> builder, 
    CancellationToken cancel = default(CancellationToken))
```

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
