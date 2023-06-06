## MongoDB x C# step by step

### Grundaufbau
- .net SDK installieren: https://dotnet.microsoft.com/en-us/download
- VS Code neu Starten
- `dotnet new console` im VS Code Terminal
- `dotnet run` programm starten
- `dotnet add package MongoDB.Driver` treiber installieren
- hello world ersetzen durch:


		
		using System;
		using MongoDB.Driver;

		namespace test
		{
				class Program
				{
						static void Main(string[] args)
						{
								MongoClient dbClient = new MongoClient("mongodb://localhost");

								var dbList = dbClient.ListDatabases().ToList();

								Console.WriteLine("The list of databases on this server is: ");
								foreach (var db in dbList)
								{
										Console.WriteLine(db);
								}
						}
				}
		}
    
    ### CRUD
    alle DBs auflisten
            
            MongoClient dbClient = new MongoClient("mongodb://localhost");
            
            var dbList = dbClient.ListDatabases().ToList();

            Console.WriteLine("The list of databases on this server is: ");
            foreach (var db in dbList)
            {
                Console.WriteLine(db);
            }
            
   collections von spezifischer DB
   
            var m165Db = dbClient.GetDatabase("M165");
            var collections = m165Db.ListCollectionNames().ToList();
            
   findOne
   
            var movieCollection = dbClient.GetDatabase("M165").GetCollection<Movie>("Movies");
            var filterFindYear = Builders<Movie>.Filter.Eq(f => f.Year, 2012);
            var resultC = movieCollection.Find(filterFindYear).FirstOrDefault();
            
   findMany
            
            var filterPierceBrosnan = Builders<Movie>.Filter.AnyEq(f => f.Actors, "Pierce Brosnan");
            var resultD = movieCollection.Find(filterPierceBrosnan).FirstOrDefault();
            
   insertOne
            
            Movie movie1 = new Movie();
            movie1.Title = "The Da Vinci Code";
            movie1.Summary = "So dunkel ist der Betrug an der Menschheit";

            var movieCollectionE = dbClient.GetDatabase("M165").GetCollection<Movie>("Movies");
            movieCollectionE.InsertOne(movie1);
            
   insertMany
   
            Movie movie2 = new Movie();
            Movie movie3 = new Movie();
            Movie[] manyMovies = new Movie[] {movie2, movie3};
            movieCollectionE.InsertMany(manyMovies);
            
   den Objekten wie oben noch die werte geben
   
   updateOne
            
            const string oldValue = "Bagels N Buns";
            const string newValue = "2 Bagels 2 Buns";
            var filter = Builders<Restaurant>.Filter
                .Eq(restaurant => restaurant.Name, oldValue);
            var update = Builders<Restaurant>.Update
                .Set(restaurant => restaurant.Name, newValue);
            movieCollectionE.UpdateOne(filter, update);
            
   updateMany
   
            const string oldValue = "Pizza";
            const string newValue = "Pasta and breadsticks";
            var filter = Builders<Restaurant>.Filter
                .Eq(restaurant => restaurant.Cuisine, oldValue);
            var update = Builders<Restaurant>.Update
                .Set(restaurant => restaurant.Cuisine, newValue);
            return _restaurantsCollection.UpdateMany(filter, update);
            
   deleteOne
            
            var filter = Builders<Restaurant>.Filter
                .Eq(r => r.Name, "Ready Penny Inn");
            return _restaurantsCollection.DeleteOne(filter);
            
   deleteMany
   
            var filter = Builders<Restaurant>.Filter
                .Eq(r => r.Borough, "Brooklyn");
            return _restaurantsCollection.DeleteMany(filter);
            
         
### Aggragationen
            var aggregateResult = moviesCollection.Aggregate()
            .Match(m => m.Year >= 2000)
            .Group( m => m.Year, g => new{ Jahr = g.Key, Anzahl=g.Count()})
            .SortBy(m => m.Jahr)
            .ToList();
