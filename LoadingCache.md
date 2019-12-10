https://www.baeldung.com/guava-cache
noteworthy points:
6. Handle null Values  8. Preload the Cache

https://guava.dev/releases/23.0/api/docs/com/google/common/cache/CacheLoader.html

java.lang.Object
com.google.common.cache.CacheLoader<K,V>

Computes or retrieves values, based on a key, for use in populating a LoadingCache.
Most implementations will only need to implement load(K). Other methods may be overridden as desired.
Usage example:
 CacheLoader<Key, Graph> loader = new CacheLoader<Key, Graph>() {
   public Graph load(Key key) throws AnyException {
     return createExpensiveGraph(key);
   }
 };
 LoadingCache<Key, Graph> cache = CacheBuilder.newBuilder().build(loader);
 
Since this example doesn't support reloading or bulk loading, it can also be specified much more simply:
 CacheLoader<Key, Graph> loader = CacheLoader.from(key -> createExpensiveGraph(key));

https://guava.dev/releases/19.0/api/docs/com/google/common/cache/LoadingCache.html
V	getUnchecked(K key)
Returns the value associated with key in this cache, first loading that value if necessary.

https://guava.dev/releases/16.0/api/docs/com/google/common/cache/CacheBuilder.html
<K1 extends K,V1 extends V> 
LoadingCache<K1,V1>	build(CacheLoader<? super K1,V1> loader)
Builds a cache, which either returns an already-loaded value for a given key or atomically computes or retrieves it using the supplied CacheLoader.

