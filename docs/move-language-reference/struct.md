# Struct

Structure is a custom type which contains complex data. It can be described as a simple key-value storage where key is a name of property and value is what's stored. Defined using keyword `struct`.

## Rules

1. Struct definition is possible **only within a module**;
2. Struct can contain field of any type including another structure;
3. Structs can only be constructed in the module in which they are declared;
4. Struct fields can only be accessed within module (therefore they are all private);
5. Recursive struct definition is impossible;
6. Struct may not have fields (can be empty).

## Definition and Usage

Again: `struct` can be defined only in module context.

```Move
module VinylShop {

    // struct can contain no fields it's
    // initialization will be like this:
    // let d = Dummy {};
    struct Dummy {}

    struct Record {
        author_id: u64,
        label_id: u64,
        year: u64,
        is_new: bool
    }

    // we can create new Record in a method
    public fun add_new(author_id: u64, label_id: u64, year: u64): Record {
        // when creating new struct use pattern:
        // <key>: <value>
        Record {
            author_id: author_id,
            label_id: label_id,
            year: year,
            is_new: true
        };

        // you can could also use short notation when variable
        // name matches name of the property
        let record: Record = Record {
            author_id,
            label_id,
            year,
            is_new: true
        };

        record
    }

    // since struct fields can only be accessed within module, you
    // have to provide interface to read structs if you pass it
    // outside of the module
    public fun print_year(r: Record): u64 {

        // to access struct's fields use "." (dot) notation
        r.year
    }
}
```

Let's take a better look at `add_new(...)` function. Since struct is a new type, it can also be a return type, and you're able to see it in function definition:

```Move
public fun add_new(author_id: u64, label_id: u64, year: u64): Record { /* ... */ }
```

Okay. How can we use our `Record` struct after we published `VinylShop` module into network? Here's how:

```Move
// specify publisher's address
use {sender}::VinylShop;

fun main() {
    // we can use type but we can't construct it - Rule #3
    let record : VinylShop::Record = VinylShop::add_new(10, 10, 1999);

    // here we can pass record type but can't use `record.year` - Rule #4
    if (VinylShop::print_year(record) == 1999) abort 11;
}
```