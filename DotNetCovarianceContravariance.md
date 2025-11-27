/*
 * .NET 8 COVARIANCE AND CONTRAVARIANCE TUTORIAL
 * 
 * Understanding variance through real-world examples
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
    
    // Example: Animal shelter that produces animals
    public interface IAnimalShelter<out T> where T : Animal
    {
        T AdoptAnimal();  // Only returns T (output position)
        IEnumerable<T> GetAvailableAnimals();  // Only returns T
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
    
    // Example: Veterinarian that treats animals
    public interface IVeterinarian<in T> where T : Animal
    {
        void Treat(T animal);  // Only accepts T (input position)
        void Vaccinate(T animal);  // Only accepts T
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
    // PART 4: BUILT-IN EXAMPLES
    // ========================================
    
    public class BuiltInExamples
    {
        public static void DemonstrateCovariance()
        {
            Console.WriteLine("\n=== BUILT-IN COVARIANCE: IEnumerable<T> ===");
            
            // IEnumerable<T> is covariant (out T)
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
            
            // Action<T> is contravariant (in T)
            Action<Animal> animalAction = (animal) => 
            {
                Console.WriteLine($"Processing animal: {animal.Name}");
            };
            
            // We can assign Action<Animal> to Action<Dog>
            // Because we're only WRITING (passing dogs in)
            Action<Dog> dogAction = animalAction;  // This works!
            
            dogAction(new Dog { Name = "Fido" });
        }
    }

    // ========================================
    // PART 5: REAL-WORLD SCENARIO
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
            Console.WriteLine("=== COVARIANCE DEMO ===");
            Console.WriteLine("Covariance: Using a MORE specific type where a GENERAL type is expected\n");
            
            // DogShelter produces Dogs, but we can treat it as producing Animals
            IAnimalShelter<Dog> dogShelter = new DogShelter();
            IAnimalShelter<Animal> animalShelter = dogShelter;  // Covariance!
            
            Animal adoptedAnimal = animalShelter.AdoptAnimal();
            Console.WriteLine($"Adopted: {adoptedAnimal.Name}");
            adoptedAnimal.MakeSound();
            
            Console.WriteLine("\n=== CONTRAVARIANCE DEMO ===");
            Console.WriteLine("Contravariance: Using a MORE general type where a SPECIFIC type is expected\n");
            
            // GeneralVeterinarian treats Animals, but we can use it to treat Dogs
            IVeterinarian<Animal> generalVet = new GeneralVeterinarian();
            IVeterinarian<Dog> dogVet = generalVet;  // Contravariance!
            
            Dog myDog = new Dog { Name = "Charlie" };
            dogVet.Treat(myDog);
            
            // Built-in examples
            BuiltInExamples.DemonstrateCovariance();
            BuiltInExamples.DemonstrateContravariance();
            
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
        }
    }
}

/*
 * MEMORY TIPS:
 * 
 * COVARIANCE (out):
 * - Think: "OUTPUT" - you're getting things OUT
 * - More specific → Less specific (Dog shelter → Animal shelter)
 * - Real world: If you can give out Dogs, you can give out Animals
 * 
 * CONTRAVARIANCE (in):
 * - Think: "INPUT" - you're putting things IN
 * - Less specific → More specific (Animal vet → Dog vet)
 * - Real world: If you can treat Animals, you can treat Dogs
 * 
 * WHY IT MATTERS:
 * - Write more flexible, reusable code
 * - Use collections and delegates more naturally
 * - Understand .NET built-in types (IEnumerable, Action, Func)
 */
