---
title: How to: Add and Remove Items from a ConcurrentDictionary
description: How to: Add and Remove Items from a ConcurrentDictionary
keywords: .NET, .NET Core
author: BillWagner
manager: wpickett
ms.date: 06/20/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: b7c04a5f-a8e6-42ae-8c84-0e1ae18896eb
---

# How to: Add and Remove Items from a ConcurrentDictionary

This example shows how to add, retrieve, update, and remove items from a [System.Collections.Concurrent.ConcurrentDictionary&lt;TKey, TValue&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Concurrent.ConcurrentDictionary%602). This collection class is a thread-safe implementation. We recommend that you use it whenever multiple threads might be attempting to access the elements concurrently.

`ConcurrentDictionary<TKey, TValue>` provides several convenience methods that make it unnecessary for code to first check whether a key exists before it attempts to add or remove data. The following table lists these convenience methods and describes when to use them.

Method | Use when...
------ | -----------
`AddOrUpdate` | You want to add a new value for a specified key and, if the key already exists, you want to replace its value.
`GetOrAdd` | You want to retrieve the existing value for a specified key and, if the key does not exist, you want to specify a key/value pair.
`TryAdd`, `TryGetValue`, `TryUpdate`, `TryRemove` | You want to add, get, update, or remove a key/value pair, and, if the key already exists or the attempt fails for any other reason, you want to take some alternative action.

## Example

```csharp
namespace DictionaryHowTo
{
    using System;
    using System.Collections.Concurrent;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;

    // The type of the Value to store in the dictionary:
    class CityInfo : IEqualityComparer<CityInfo>
    {
        public string Name { get; set; }
        public DateTime lastQueryDate { get; set; }
        public decimal Longitude { get; set; }
        public decimal Latitude { get; set; }
        public int[] RecentHighTemperatures { get; set; }

        public CityInfo(string name, decimal longitude, decimal latitude, int[] temps)
        {
            Name = name;
            lastQueryDate = DateTime.Now;
            Longitude = longitude;
            Latitude = latitude;
            RecentHighTemperatures = temps;
        }

        public CityInfo()
        {
        }

        public CityInfo(string key)
        {
            Name = key;
            // MaxValue means "not initialized"
            Longitude = Decimal.MaxValue;
            Latitude = Decimal.MaxValue;
            lastQueryDate = DateTime.Now;
            RecentHighTemperatures = new int[] { 0 };

        }
        public bool Equals(CityInfo x, CityInfo y)
        {
            return x.Name == y.Name && x.Longitude == y.Longitude && x.Latitude == y.Latitude;
        }

        public int GetHashCode(CityInfo obj)
        {
            CityInfo ci = (CityInfo)obj;
            return ci.Name.GetHashCode();
        }
    }

    class Program
    {
        // Create a new concurrent dictionary.
        static ConcurrentDictionary<string, CityInfo> cities = new ConcurrentDictionary<string, CityInfo>();

        static void Main(string[] args)
        {
            CityInfo[] data = 
            {
                new CityInfo(){ Name = "Boston", Latitude = 42.358769M, Longitude = -71.057806M, RecentHighTemperatures = new int[] {56, 51, 52, 58, 65, 56,53}},
                new CityInfo(){ Name = "Miami", Latitude = 25.780833M, Longitude = -80.195556M, RecentHighTemperatures = new int[] {86,87,88,87,85,85,86}},
                new CityInfo(){ Name = "Los Angeles", Latitude = 34.05M, Longitude = -118.25M, RecentHighTemperatures =   new int[] {67,68,69,73,79,78,78}},
                new CityInfo(){ Name = "Seattle", Latitude = 47.609722M, Longitude =  -122.333056M, RecentHighTemperatures =   new int[] {49,50,53,47,52,52,51}},
                new CityInfo(){ Name = "Toronto", Latitude = 43.716589M, Longitude = -79.340686M, RecentHighTemperatures =   new int[] {53,57, 51,52,56,55,50}},
                new CityInfo(){ Name = "Mexico City", Latitude = 19.432736M, Longitude = -99.133253M, RecentHighTemperatures =   new int[] {72,68,73,77,76,74,73}},
                new CityInfo(){ Name = "Rio de Janiero", Latitude = -22.908333M, Longitude = -43.196389M, RecentHighTemperatures =   new int[] {72,68,73,82,84,78,84}},
                new CityInfo(){ Name = "Quito", Latitude = -0.25M, Longitude = -78.583333M, RecentHighTemperatures =   new int[] {71,69,70,66,65,64,61}}
            };

            // Add some key/value pairs from multiple threads.
            Task[] tasks = new Task[2];

            tasks[0] = Task.Run(() =>
            {
                for (int i = 0; i < 2; i++)
                {
                    if (cities.TryAdd(data[i].Name, data[i]))
                        Console.WriteLine("Added {0} on thread {1}", data[i],
                            Thread.CurrentThread.ManagedThreadId);
                    else 
                        Console.WriteLine("Could not add {0}", data[i]);
                }
            });

            tasks[1] = Task.Run(() =>
            {
                for (int i = 2; i < data.Length; i++)
                {
                    if (cities.TryAdd(data[i].Name, data[i]))
                        Console.WriteLine("Added {0} on thread {1}", data[i],
                            Thread.CurrentThread.ManagedThreadId);
                    else
                        Console.WriteLine("Could not add {0}", data[i]);
                }
            });

            // Output results so far.
            Task.WaitAll(tasks);

            // Enumerate collection from the app main thread.
            // Note that ConcurrentDictionary is the one concurrent collection
            // that does not support thread-safe enumeration.
            foreach (var city in cities)
            {
                Console.WriteLine("{0} has been added.", city.Key);
            }

            AddOrUpdateWithoutRetrieving();
            RetrieveValueOrAdd();
            RetrieveAndUpdateOrAdd();  

            Console.WriteLine("Press any key.");
            Console.ReadKey();
        }

        // This method shows how to add key-value pairs to the dictionary
        // in scenarios where the key might already exist.
        private static void AddOrUpdateWithoutRetrieving()
        {
            // Sometime later. We receive new data from some source.
            CityInfo ci = new CityInfo() { Name = "Toronto",
                                            Latitude = 43.716589M,
                                            Longitude = -79.340686M,
                                            RecentHighTemperatures = new int[] { 54, 59, 67, 82, 87, 55, -14 } };

            // Try to add data. If it doesn't exist, the object ci is added. If it does
            // already exist, update existingVal according to the custom logic in the 
            // delegate.
            cities.AddOrUpdate(ci.Name, ci,
                (key, existingVal) =>
                {
                    // If this delegate is invoked, then the key already exists.
                    // Here we make sure the city really is the same city we already have.
                    // (Support for multiple cities of the same name is left as an exercise for the reader.)
                    if (ci != existingVal)
                        throw new ArgumentException("Duplicate city names are not allowed: {0}.", ci.Name);

                    // The only updatable fields are the temerature array and lastQueryDate.
                    existingVal.lastQueryDate = DateTime.Now;
                    existingVal.RecentHighTemperatures = ci.RecentHighTemperatures;
                    return existingVal;
                });

            // Verify that the dictionary contains the new or updated data.
            Console.Write("Most recent high temperatures for {0} are: ", cities[ci.Name].Name);
            int[] temps = cities[ci.Name].RecentHighTemperatures;
            foreach (var temp in temps) Console.Write("{0}, ", temp);
            Console.WriteLine();
        }

        // This method shows how to use data and ensure that it has been
        // added to the dictionary.
        private static void RetrieveValueOrAdd()
        {
            string searchKey = "Caracas";
            CityInfo retrievedValue = null;

            try
            {
                retrievedValue = cities.GetOrAdd(searchKey, GetDataForCity(searchKey));
            }
            catch (ArgumentException e)
            {
                Console.WriteLine(e.Message);
            }

            // Use the data.
            if (retrievedValue != null)
            {
                Console.Write("Most recent high temperatures for {0} are: ", retrievedValue.Name);
                int[] temps = cities[retrievedValue.Name].RecentHighTemperatures;
                foreach (var temp in temps) Console.Write("{0}, ", temp);
            }
            Console.WriteLine();
        }




        // This method shows how to retrieve a value from the dictionary,
        // when you expect that the key/value pair already exists,
        // and then possibly update the dictionary with a new value for the key.
        private static void RetrieveAndUpdateOrAdd()
        {
            CityInfo retrievedValue;
            string searchKey = "Buenos Aires";

            if (cities.TryGetValue(searchKey, out retrievedValue))
            {
                // use the data
                Console.Write("Most recent high temperatures for {0} are: ", retrievedValue.Name);
                int[] temps = retrievedValue.RecentHighTemperatures;
                foreach (var temp in temps) Console.Write("{0}, ", temp);

                // Make a copy of the data. Our object will update its lastQueryDate automatically.
                CityInfo newValue = new CityInfo(retrievedValue.Name,
                                                retrievedValue.Longitude,
                                                retrievedValue.Latitude,
                                                retrievedValue.RecentHighTemperatures);

                // Replace the old value with the new value.
                if (!cities.TryUpdate(searchKey, retrievedValue, newValue))
                {
                    //The data was not updated. Log error, throw exception, etc.
                    Console.WriteLine("Could not update {0}", retrievedValue.Name);
                }
            }
            else
            {
                // Add the new key and value. Here we call a method to retrieve
                // the data. Another option is to add a default value here and 
                // update with real data later on some other thread.
                CityInfo newValue = GetDataForCity(searchKey);
                if( cities.TryAdd(searchKey, newValue))
                {
                    // use the data
                    Console.Write("Most recent high temperatures for {0} are: ", newValue.Name);
                    int[] temps = newValue.RecentHighTemperatures;
                    foreach (var temp in temps) Console.Write("{0}, ", temp);
                }
                else
                    Console.WriteLine("Unable to add data for {0}", searchKey);
            }
        }

        //Assume this method knows how to find long/lat/temp info for any specified city.
        static CityInfo GetDataForCity(string name)
        {
            // Real implementation left as exercise for the reader.
            if (String.CompareOrdinal(name, "Caracas") == 0)
                return new CityInfo() { Name = "Caracas", 
                                        Longitude = 10.5M, 
                                        Latitude = -66.916667M,
                                        RecentHighTemperatures = new int[] { 91, 89, 91, 91, 87, 90, 91 } };
            else if (String.CompareOrdinal(name, "Buenos Aires") == 0)
                return new CityInfo() { Name = "Buenos Aires", 
                                        Longitude = -34.61M, 
                                        Latitude = -58.369997M, 
                                        RecentHighTemperatures = new int[] { 80, 86, 89, 91, 84, 86, 88 } };
            else
                throw new ArgumentException("Cannot find any data for {0}", name);
        }
    }
}

```
[ConcurrentDictionary&lt;TKey, TValue&gt;](https://docs.microsoft.com/dotnet/core/api/System.Collections.Concurrent.ConcurrentDictionary%602) is designed for multithreaded scenarios. You do not have to use locks in your code to add or remove items from the collection. However, it is always possible for one thread to retrieve a value, and another thread to immediately update the collection by giving the same key a new value.

Also, although all methods of `ConcurrentDictionary<TKey, TValue>` are thread-safe, not all methods are atomic, specifically `GetOrAdd` and `AddOrUpdate`. The user delegate that is passed to these methods is invoked outside of the dictionary's internal lock. (This is done to prevent unknown code from blocking all threads.) Therefore it is possible for this sequence of events to occur:

1. threadA calls `GetOrAdd`, finds no item and creates a new item to Add by invoking the valueFactory delegate.

2. threadB calls `GetOrAdd` concurrently, its valueFactory delegate is invoked and it arrives at the internal lock before threadA, and so its new key-value pair is added to the dictionary.

3. threadA's user delegate completes, and the thread arrives at the lock, but now sees that the item exists already

4. threadA performs a "Get", and returns the data that was previously added by threadB.

Therefore, it is not guaranteed that the data that is returned by `GetOrAdd` is the same data that was created by the thread's valueFactory. A similar sequence of events can occur when `AddOrUpdate` is called. 

## See Also

[System.Collections.Concurrent](https://docs.microsoft.com/dotnet/core/api/System.Collections.Concurrent)

[Thread-Safe Collections](../thread-safe-collections.md)

