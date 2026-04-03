/*
 * .NET 8 COVARIANCE AND CONTRAVARIANCE TUTORIAL
 * 
 * Understanding variance through real-world examples
 * 
 * OFFICIAL REFERENCES:
 * 
 * Microsoft Learn Documentation:
 * 1. Covariance and Contravariance (C#)
 *    https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/concepts/covariance-contravariance/
 * 
 * 2. Variance in Delegates (C#)
 *    https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/concepts/covariance-contravariance/variance-in-delegates
 * 
 * 3. Using Variance in Delegates (C#)
 *    https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/concepts/covariance-contravariance/using-variance-in-delegates
 * 
 * 4. Using Variance for Func and Action Generic Delegates (C#)
 *    https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/concepts/covariance-contravariance/using-variance-for-func-and-action-generic-delegates
 * 
 * 5. Covariance and Contravariance in Generics (.NET)
 *    https://learn.microsoft.com/en-us/dotnet/standard/generics/covariance-and-contravariance
 * 
 * 6. Variance in Generic Interfaces (C#)
 *    https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/concepts/covariance-contravariance/variance-in-generic-interfaces
 * 
 * 7. Creating Variant Generic Interfaces (C#)
 *    https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/concepts/covariance-contravariance/creating-variant-generic-interfaces
 */

using System;
using System.Collections.Generic;

namespace VarianceTutorial
{
    // ========================================
    // PART 1: CLASS HIERARCHY SETUP
    // ========================================
    
    // Base class representing any animal
    public class Animal
    {
        public string Name { get; set; }
        public virtual void MakeSound() => Console.WriteLine("Some sound");
    }
    
    // Dog is more specific than Animal
    public class Dog : Animal
    {
        public override void MakeSound() => Console.WriteLine("Woof!");
        public void Fetch() => Console.WriteLine($"{Name} is fetching!");
    }
    
    // Cat is more specific than Animal
    public class Cat : Animal
    {
        public override void MakeSound() => Console.WriteLine("Meow!");
        public void Climb() => Console.WriteLine($"{Name} is climbing!");
    }

    // ========================================
    // PART 2: COVARIANCE (out keyword)
    // ========================================
    // Covariance allows you to use a MORE DERIVED type than originally specified
    // Think: "I can give you something more specific than you asked for"
    
    // WITHOUT 'out' keyword - This would NOT allow covariance
    public interface INonVariantShelter<T> where T : Animal
    {
        T AdoptAnimal();
    }
    
    // WITH 'out' keyword - This ENABLES covariance
    // The 'out' keyword is MANDATORY if you want covariance to work
    public interface IAnimalShelter<out T> where T : Animal
    {
        T AdoptAnimal();  // Only returns T (output position)
        IEnumerable<T> GetAvailableAnimals();  // Only returns T
        
        // COMPILER ERROR if you uncomment this - 'out' parameters can't be in input positions:
        // void AddAnimal(T animal);  // ❌ Won't compile with 'out T'
    }
    
    public class DogShelter : IAnimalShelter<Dog>
    {
        private List<Dog> dogs = new()
        {
            new Dog { Name = "Buddy" },
            new Dog { Name = "Max" }
        };
        
        public Dog AdoptAnimal() => dogs[0];
        public IEnumerable<Dog> GetAvailableAnimals() => dogs;
    }
    
    public class CatShelter : IAnimalShelter<Cat>
    {
        private List<Cat> cats = new()
        {
            new Cat { Name = "Whiskers" },
            new Cat { Name = "Luna" }
        };
        
        public Cat AdoptAnimal() => cats[0];
        public IEnumerable<Cat> GetAvailableAnimals() => cats;
    }

    // ========================================
    // PART 3: CONTRAVARIANCE (in keyword)
    // ========================================
    // Contravariance allows you to use a LESS DERIVED type than originally specified
    // Think: "I can accept something more general than you specified"
    
    // WITHOUT 'in' keyword - This would NOT allow contravariance
    public interface INonVariantVeterinarian<T> where T : Animal
    {
        void Treat(T animal);
    }
    
    // WITH 'in' keyword - This ENABLES contravariance
    // The 'in' keyword is MANDATORY if you want contravariance to work
    public interface IVeterinarian<in T> where T : Animal
    {
        void Treat(T animal);  // Only accepts T (input position)
        void Vaccinate(T animal);  // Only accepts T
        
        // COMPILER ERROR if you uncomment this - 'in' parameters can't be in output positions:
        // T GetPatient();  // ❌ Won't compile with 'in T'
    }
    
    // A general vet can treat any animal
    public class GeneralVeterinarian : IVeterinarian<Animal>
    {
        public void Treat(Animal animal)
        {
            Console.WriteLine($"Treating {animal.Name}");
            animal.MakeSound();
        }
        
        public void Vaccinate(Animal animal)
        {
            Console.WriteLine($"Vaccinating {animal.Name}");
        }
    }
    
    // A specialist vet for dogs
    public class DogVeterinarian : IVeterinarian<Dog>
    {
        public void Treat(Dog dog)
        {
            Console.WriteLine($"Treating dog {dog.Name} with specialized care");
            dog.Fetch();
        }
        
        public void Vaccinate(Dog dog)
        {
            Console.WriteLine($"Giving dog-specific vaccine to {dog.Name}");
        }
    }

    // ========================================
    // PART 4: DELEGATE VARIANCE
    // ========================================
    
    // Delegates support variance too!
    // Remember: 'out' for return types (covariance), 'in' for parameters (contravariance)
    
    // COVARIANT DELEGATE - returns T (output position)
    // Can return more specific types than specified
    public delegate T AnimalFactory<out T>() where T : Animal;
    
    // CONTRAVARIANT DELEGATE - accepts T (input position)
    // Can accept more general types than specified
    public delegate void AnimalHandler<in T>(T animal) where T : Animal;
    
    // MIXED VARIANCE - combines both!
    // TResult is covariant (out), TInput is contravariant (in)
    public delegate TResult AnimalProcessor<in TInput, out TResult>(TInput animal) 
        where TInput : Animal 
        where TResult : class;
    
    public class DelegateExamples
    {
        // Factory methods for covariance demo
        public static Dog CreateDog() => new Dog { Name = "Rover" };
        public static Cat CreateCat() => new Cat { Name = "Mittens" };
        
        // Handler methods for contravariance demo
        public static void HandleAnimal(Animal animal)
        {
            Console.WriteLine($"Handling any animal: {animal.Name}");
            animal.MakeSound();
        }
        
        public static void HandleDog(Dog dog)
        {
            Console.WriteLine($"Handling dog specifically: {dog.Name}");
            dog.Fetch();
        }
        
        // Processor for mixed variance demo
        public static string ProcessAnimal(Animal animal)
        {
            return $"Processed {animal.Name}";
        }
        
        public static void DemonstrateDelegateVariance()
        {
            Console.WriteLine("\n=== DELEGATE COVARIANCE ===");
            Console.WriteLine("Factory that creates Dogs can be used as factory that creates Animals\n");
            
            // Covariance: Dog factory → Animal factory
            AnimalFactory<Dog> dogFactory = CreateDog;
            AnimalFactory<Animal> animalFactory = dogFactory;  // Covariance!
            
            Animal animal = animalFactory();
            Console.WriteLine($"Created via variant delegate: {animal.Name}");
            
            Console.WriteLine("\n=== DELEGATE CONTRAVARIANCE ===");
            Console.WriteLine("Handler that accepts Animals can be used as handler that accepts Dogs\n");
            
            // Contravariance: Animal handler → Dog handler
            AnimalHandler<Animal> animalHandler = HandleAnimal;
            AnimalHandler<Dog> dogHandler = animalHandler;  // Contravariance!
            
            dogHandler(new Dog { Name = "Buddy" });
            
            Console.WriteLine("\n=== DELEGATE MIXED VARIANCE ===");
            Console.WriteLine("Processor with contravariant input and covariant output\n");
            
            // Mixed variance: Animal→string processor can be used as Dog→object processor
            AnimalProcessor<Animal, string> animalProcessor = ProcessAnimal;
            
            // Can assign to:
            // - More specific input (Animal → Dog) = contravariance on input
            // - Less specific output (string → object) = covariance on output
            AnimalProcessor<Dog, object> dogProcessor = animalProcessor;  // Both variances!
            
            object result = dogProcessor(new Dog { Name = "Max" });
            Console.WriteLine($"Result: {result}");
        }
    }
    
    // ========================================
    // PART 5: BUILT-IN DELEGATE EXAMPLES
    // ========================================
    
    public class BuiltInExamples
    {
        public static void DemonstrateCovariance()
        {
            Console.WriteLine("\n=== BUILT-IN COVARIANCE: IEnumerable<T> ===");
            
            // IEnumerable<out T> is covariant
            IEnumerable<Dog> dogs = new List<Dog> 
            { 
                new Dog { Name = "Rex" },
                new Dog { Name = "Spot" }
            };
            
            // We can assign IEnumerable<Dog> to IEnumerable<Animal>
            // Because we're only READING (getting animals out)
            IEnumerable<Animal> animals = dogs;  // This works!
            
            foreach (var animal in animals)
            {
                Console.WriteLine($"Found animal: {animal.Name}");
            }
        }
        
        public static void DemonstrateContravariance()
        {
            Console.WriteLine("\n=== BUILT-IN CONTRAVARIANCE: Action<T> ===");
            
            // Action<in T> is contravariant (delegate defined as: delegate void Action<in T>(T obj))
            Action<Animal> animalAction = (animal) => 
            {
                Console.WriteLine($"Processing animal: {animal.Name}");
            };
            
            // We can assign Action<Animal> to Action<Dog>
            // Because we're only WRITING (passing dogs in)
            Action<Dog> dogAction = animalAction;  // This works!
            
            dogAction(new Dog { Name = "Fido" });
        }
        
        public static void DemonstrateFuncVariance()
        {
            Console.WriteLine("\n=== BUILT-IN MIXED VARIANCE: Func<T, TResult> ===");
            Console.WriteLine("Func<in T, out TResult> has BOTH variance types!\n");
            
            // Func<in T, out TResult> - T is contravariant, TResult is covariant
            // Actual definition: delegate TResult Func<in T, out TResult>(T arg)
            Func<Animal, Dog> animalToDog = (animal) => new Dog { Name = $"Dog-{animal.Name}" };
            
            // Can assign to Func<Dog, Animal> because:
            // - Input: Animal → Dog (contravariance - can accept more specific input)
            // - Output: Dog → Animal (covariance - can return more specific output)
            Func<Dog, Animal> dogToAnimal = animalToDog;  // Both variances!
            
            Animal result = dogToAnimal(new Dog { Name = "Input" });
            Console.WriteLine($"Result: {result.Name}");
            
            // PRACTICAL EXAMPLE: LINQ
            Console.WriteLine("\nPractical LINQ example:");
            List<Dog> dogList = new List<Dog> 
            { 
                new Dog { Name = "Alpha" }, 
                new Dog { Name = "Beta" } 
            };
            
            // This works because Func<Dog, string> can be used as Func<Animal, string>
            // due to contravariance on input parameter
            Func<Animal, string> getName = (animal) => animal.Name;
            var names = dogList.Select(getName);  // Works perfectly!
            
            Console.WriteLine($"Dog names: {string.Join(", ", names)}");
        }
    }

    // ========================================
    // PART 6: REAL-WORLD SCENARIO
    // ========================================
    
    // Document management system example
    public class Document { public string Title { get; set; } }
    public class PDFDocument : Document { public int PageCount { get; set; } }
    public class WordDocument : Document { public bool HasTrackChanges { get; set; } }
    
    // Covariant: Document repository that produces documents
    public interface IDocumentRepository<out T> where T : Document
    {
        T GetById(int id);
        IEnumerable<T> GetAll();
    }
    
    // Contravariant: Document processor that consumes documents
    public interface IDocumentProcessor<in T> where T : Document
    {
        void Process(T document);
        void Archive(T document);
    }
    
    public class PDFRepository : IDocumentRepository<PDFDocument>
    {
        public PDFDocument GetById(int id) => new PDFDocument { Title = "Sample.pdf", PageCount = 10 };
        public IEnumerable<PDFDocument> GetAll() => new List<PDFDocument>();
    }
    
    public class GeneralDocumentProcessor : IDocumentProcessor<Document>
    {
        public void Process(Document doc) => Console.WriteLine($"Processing: {doc.Title}");
        public void Archive(Document doc) => Console.WriteLine($"Archiving: {doc.Title}");
    }

    // ========================================
    // DEMONSTRATION
    // ========================================
    
    class Program
    {
        static void Main()
        {
            Console.WriteLine("=== WHY 'in' AND 'out' ARE MANDATORY ===\n");
            
            // WITHOUT variance keywords, this won't compile:
            // INonVariantShelter<Dog> nonVariantDogShelter = new DogShelter();
            // INonVariantShelter<Animal> nonVariantAnimalShelter = nonVariantDogShelter;  // ❌ COMPILER ERROR!
            
            Console.WriteLine("Without 'out' keyword: Cannot assign IInterface<Dog> to IInterface<Animal>");
            Console.WriteLine("Without 'in' keyword: Cannot assign IInterface<Animal> to IInterface<Dog>");
            Console.WriteLine("\nThe keywords are MANDATORY to enable variance!\n");
            
            Console.WriteLine("=== COVARIANCE DEMO ===");
            Console.WriteLine("Covariance: Using a MORE specific type where a GENERAL type is expected\n");
            
            // DogShelter produces Dogs, but we can treat it as producing Animals
            // This ONLY works because we declared IAnimalShelter<out T>
            IAnimalShelter<Dog> dogShelter = new DogShelter();
            IAnimalShelter<Animal> animalShelter = dogShelter;  // Covariance!
            
            Animal adoptedAnimal = animalShelter.AdoptAnimal();
            Console.WriteLine($"Adopted: {adoptedAnimal.Name}");
            adoptedAnimal.MakeSound();
            
            Console.WriteLine("\n=== CONTRAVARIANCE DEMO ===");
            Console.WriteLine("Contravariance: Using a MORE general type where a SPECIFIC type is expected\n");
            
            // GeneralVeterinarian treats Animals, but we can use it to treat Dogs
            // This ONLY works because we declared IVeterinarian<in T>
            IVeterinarian<Animal> generalVet = new GeneralVeterinarian();
            IVeterinarian<Dog> dogVet = generalVet;  // Contravariance!
            
            Dog myDog = new Dog { Name = "Charlie" };
            dogVet.Treat(myDog);
            
            // Built-in examples
            BuiltInExamples.DemonstrateCovariance();
            BuiltInExamples.DemonstrateContravariance();
            BuiltInExamples.DemonstrateFuncVariance();
            
            // Delegate examples
            DelegateExamples.DemonstrateDelegateVariance();
            
            Console.WriteLine("\n=== DOCUMENT SYSTEM DEMO ===");
            
            // Covariance: PDF repository can be used as Document repository
            IDocumentRepository<PDFDocument> pdfRepo = new PDFRepository();
            IDocumentRepository<Document> docRepo = pdfRepo;  // Covariance!
            Document doc = docRepo.GetById(1);
            Console.WriteLine($"Retrieved document: {doc.Title}");
            
            // Contravariance: General processor can be used as PDF processor
            IDocumentProcessor<Document> generalProcessor = new GeneralDocumentProcessor();
            IDocumentProcessor<PDFDocument> pdfProcessor = generalProcessor;  // Contravariance!
            pdfProcessor.Process(new PDFDocument { Title = "Report.pdf", PageCount = 5 });
            
            Console.WriteLine("\n=== KEY TAKEAWAYS ===");
            Console.WriteLine("• COVARIANCE (out): Returns more specific types → IEnumerable<Dog> to IEnumerable<Animal>");
            Console.WriteLine("• CONTRAVARIANCE (in): Accepts more general types → Action<Animal> to Action<Dog>");
            Console.WriteLine("• Rule of thumb: 'out' = output only, 'in' = input only");
            Console.WriteLine("• The 'in' and 'out' keywords are MANDATORY - without them, variance won't work!");
        }
    }
}

/*
 * MEMORY TIPS:
 * 
 * ARE 'in' AND 'out' MANDATORY?
 * YES! Without them, variance doesn't work at all.
 * - No 'out'? → Cannot assign IInterface<Dog> to IInterface<Animal>
 * - No 'in'? → Cannot assign IInterface<Animal> to IInterface<Dog>
 * 
 * COVARIANCE (out):
 * - Think: "OUTPUT" - you're getting things OUT
 * - More specific → Less specific (Dog shelter → Animal shelter)
 * - Real world: If you can give out Dogs, you can give out Animals
 * - Restrictions: T can ONLY appear in output positions (return types)
 * - Examples: IEnumerable<out T>, Func<out TResult>
 * 
 * CONTRAVARIANCE (in):
 * - Think: "INPUT" - you're putting things IN
 * - Less specific → More specific (Animal vet → Dog vet)
 * - Real world: If you can treat Animals, you can treat Dogs
 * - Restrictions: T can ONLY appear in input positions (parameters)
 * - Examples: Action<in T>, IComparer<in T>
 * 
 * DELEGATE VARIANCE:
 * - Delegates can be variant just like interfaces
 * - Action<in T>: contravariant (only input)
 * - Func<out TResult>: covariant (only output)
 * - Func<in T, out TResult>: BOTH! (contravariant input, covariant output)
 * - Custom delegates: use 'in' and 'out' keywords when declaring
 * 
 * WHY IT MATTERS:
 * - Write more flexible, reusable code
 * - Use collections and delegates more naturally
 * - Understand .NET built-in types (IEnumerable, Action, Func)
 * - Essential for LINQ, event handlers, and functional programming patterns
 * 
 * IMPORTANT NOTES:
 * - Only works with interfaces and delegates, NOT classes
 * - Classes in C# are INVARIANT by default and cannot be made variant
 * - The compiler enforces the rules - it won't let you misuse variance
 * - Array covariance exists but is problematic (can cause runtime errors)
 */
