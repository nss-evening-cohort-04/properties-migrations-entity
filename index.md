class: center, middle

# Properties & Migrations

---

## Goals

- Review expressing models in Code
- Model Relationships
- Introduction to Migrations

---

## Models as Code

Things to know:

1. Models/Entities are objects in C# as concrete classes
1. Attributes are properties
1. Relationships are C# collections or simple references
1. properties that reference other C# classes are called **Navigation Properties**
1. properties that hold data are called **Scalar Properties**


---

## Models as Code


```c#
public class TrelloUser {

    [Key]
    public int TrelloUserId { get; set; }  // Primary Key
    public string Email { get; set; } 
    public string Username { get; set; } 
    public string FullName { get; set; } 

}
```
---

## Models as Code


```c#
public class TrelloUser {

    [Key]
    public int TrelloUserId { get; set; }  // Primary Key
*   public string Email { get; set; } 
*   public string Username { get; set; } 
*   public string FullName { get; set; } 

}
```
.center.larger[`Email`, `Username` and `FullName` are **Scalar Properties**]

---

## Models as Code

```c#
public class TrelloUser {

    [Key]
    public int TrelloUserId { get; set; }
    public string Email { get; set; } 
    public string Username { get; set; } 
    public string FullName { get; set; } 

}

public class Board {

    [Key]
    public int BoardId { get; set; }
    public string Name { get; set; }
    public string URL { get; set; } 

}
```
---

## Models as Code

```c#
public class TrelloUser {

    [Key]
    public int TrelloUserId { get; set; }
    public string Email { get; set; } 
    public string Username { get; set; } 
    public string FullName { get; set; } 
*   public ICollection<Board> Boards { get; set; }
}

public class Board {

    [Key]
    public int BoardId { get; set; }
    public string Name { get; set; }
    public string URL { get; set; } 
*   public TrelloUser Owner { get; set; }
}
```

.center.larger[`Owner` and `Boards` are **Navigation Properties**]


---

## Model Relationships as Code

.center.larger[**Navigation Properties** are used to both define and traverse model relationships]

---

## Model Relationships as Code

### One to One


```c#
public class Author {

    [Key]
    public int AuthorId { get; set; }
   
*   public SocialSecurity SSN { get; set; }
}


public class SocialSecurity {

    [Key]
    public int SocialSecurityId { get; set; }
   
*   public Author Person { get; set; }
}
```

---

## Model Relationships as Code

### One to Many


```c#
public class PublishedWork {

    [Key]
    public int AuthorId { get; set; }
   
*   public CitationStyle CiteStyle { get; set; }
}


public class CitationStyle{

    [Key]
    public int CitationStyleId { get; set; }
   
*   public ICollection<PublishedWork> WorksUsingThisStyle { get; set; }
}
```

---

## Model Relationships as Code

### Many to Many


```c#
public class Author {

    [Key]
    public int AuthorId { get; set; }
   
*   public ICollection<PublishedWork> Works { get; set; }
}


public class PublishedWork {

    [Key]
    public int PublishedWorkId { get; set; }
   
*   public ICollection<Author> Authors { get; set; }
}
```

---

class: center,middle

## Code Along: Let's finish FakeTrello models

---

class: center,middle

## On your own: Complete the models for FakeBlog

---

## Entity: Microsoft's ORM (in brief)

1. ORM stands for **Object-Relational Mapper**

--

1. Allows developer to interact with database tables using C# classes

--

1. Allows developer to modify database schemas

--

1. Interprets **Naviagtion Properties** in order to build relationships between tables

--

1. Translates C# code to SQL statements

---

## Connecting to Entity


.center.larger[**DbContext** is a special class that handles database connections]

--

.center.larger[There are multiple **\*DbContext** classes]

---

## Connecting to Entity

.center.larger[Inherit from `ApplicationDbContext`]

```c#
    public class MyAwesomeContext : ApplicationDbContext {
*       public virtual DbSet<Author> Authors { get; set; }
    }
```

.center[**DbSet** tells Entity how to make models to database tables]

--
.center[

```ruby
Enable-Migrations -ContentTypeName MyAwesomeContext
```
]

---

## Connecting to Entity

### Practice: FakeTrello

.center.larger[
Let's make a **DbContext** (child) class
]

---

## Connecting to Entity

### Practice: FakeBlog

.center.larger[
Make a **DbContext** (child) class for your blog
]

--

.center[
Do not forget to run afterwards:

```ruby
Enable-Migrations -ContentTypeName NameOfYourContextClassHere
```
]

---

## Migrations

.center.larger[**Migration**: versionable code used to modify the state of a database]

--

There are 2 kinds:

1. Data

--

1. Schema (structure)

---

## Migrations

Key Terms:

- **Zero State** - an empty database void of both data and structure

--

- **Initial State** - a database with it's first migration applied

--

- **migrate** - to transition between two database states

---

## Migrations

<div class="mermaid">
graph BT;
s0[zero state]
</div>

--

.center.larger[an empty database; no data; no structure]

---

## Migrations

<div class="mermaid">
graph BT;
s0[zero state]-->|migrate up|s1[initial state]
</div>

--

.center.larger[database now has structure]

---

## Migrations

<div class="mermaid">
graph BT;
s0[zero state]-->|migrate up|s1[initial state]
s1-->|migrate up|s2[a subsequent state]
</div>

.center.larger[upward migrations apply the most recently created states]

---

## Migrations

<div class="mermaid">
graph TB;
s2[a subsequent state]-->|migrate down|s1[initial state]
s1-->|migrate down|s0[zero state]
</div>

.center.larger[downward migrations applies the older states]

---

## Migrations

<div class="mermaid">
graph BT;
s0[zero state: s0]-->|Update-Database -Target s1|s1[initial state: s1]
s1-->|Update-Database -Target s2|s2[subsequent state: s2]
s0-->|Update-Database|s2
</div>

---

## Migrations

<div class="mermaid">
graph TB;
s2[subsequent state: s2]-->|Update-Database -Target s1|s1[initial state: s1]
s1-->|Update-Database -Target s0|s0[zero state: s0]
s2-->|Update-Database -Target $InitialDatabase|s0
</div>

