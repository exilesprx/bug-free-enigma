## Cheatsheet

## Pointers
```
//     FREE ZIG POINTER CHEATSHEET! (Using u8 as the example type.)
//   +---------------+----------------------------------------------+
//   |  u8           |  one u8                                      |
//   |  *u8          |  pointer to one u8                           |
//   |  [2]u8        |  two u8s                                     |
//   |  [*]u8        |  pointer to unknown number of u8s            |
//   |  [*]const u8  |  pointer to unknown number of immutable u8s  |
//   |  *[2]u8       |  pointer to an array of 2 u8s                |
//   |  *const [2]u8 |  pointer to an immutable array of 2 u8s      |
//   |  []u8         |  slice of u8s                                |
//   |  []const u8   |  slice of immutable u8s                      |
//   +---------------+----------------------------------------------+
```

## Tagged unions - exercise 057
```
// If you don't have a need for a separate enum, you can define
// an inferred enum with your union all in one place. Just use
// the 'enum' keyword in place of the tag type:
//
//     const Foo = union(enum) {
//         small: u8,
//         medium: u32,
//         large: u64,
//     };
```

## Error unions
```
// If canFail() fails, foo will equal 6.
// One way to deal with error unions is to "catch" any error and
// replace it with a default value.
//
//     foo = canFail() catch 6;


// Zig lets us make what's called an "error union" which is a value
// which could either be a regular value OR an error from a set:
//
//     var text: MyErrorSet!Text = getText("foo.txt");

// Catch lets us capture the error value and perform additional
// actions with this form:
//
//     canFail() catch |err| {
//         if (err == FishError.TunaMalfunction) {
//             ...
//         }
//     };

//
// Zig has a handy "try" shortcut for this common error handling pattern:
//
//     canFail() catch |err| return err;
//
// which can be more compactly written as:
//
//     try canFail();

//
// Let's revisit the very first error exercise. This time, we're going to
// look at an error-handling variation of the "if" statement.
//
//     if (foo) |value| {
//
//         // foo was NOT an error; value is the non-error value of foo
//
//     } else |err| {
//
//         // foo WAS an error; err is the error value of foo
//
//     }
//
// We'll take it even further and use a switch statement to handle
// the error types.
//
//     if (foo) |value| {
//         ...
//     } else |err| switch(err) {
//         ...
//     }
```

## Optionals
```
// Sometimes you know that a variable might hold a value or
// it might not. Zig has a neat way of expressing this idea
// called Optionals. An optional type just has a '?' like this:
//
//     var foo: ?u32 = 10;
//
// Now foo can store a u32 integer OR null (a value storing
// the cosmic horror of a value NOT EXISTING!)

// The handy ".?" shortcut:
//
//     const foo = bar.?;
//
// is the same as
//
//     const foo = bar orelse unreachable;
//

// Before we can use the optional value as the non-null type
// (a u32 integer in this case), we need to guarantee that it
// isn't null. One way to do this is to THREATEN IT with the
// "orelse" statement.
//
//     var bar = foo orelse 2;
//

// Optionals are a lot like error union types which can either
// hold a value or an error. Likewise, the orelse statement is
// like the catch statement used to "unwrap" a value or supply
// a default value:
//
//    var maybe_bad: Error!u32 = Error.Evil;
//    var number: u32 = maybe_bad catch 0;
```

## Floating point numbers
```
// As an example, Zig's f16 is a IEEE 754 "half-precision" binary
// floating-point format ("binary16"), which is stored in memory
// like so:
//
//         0 1 0 0 0 0 1 0 0 1 0 0 1 0 0 0
//         | |-------| |-----------------|
//         |  exponent     significand
//         |
//          sign
```

## Coerce
```
//
// 1. Types can always be made _more_ restrictive.
//
//    var foo: u8 = 5;
//    var p1: *u8 = &foo;
//    var p2: *const u8 = p1; // mutable to immutable
//
// 2. Numeric types can coerce to _larger_ types.
//
//    var n1: u8 = 5;
//    var n2: u16 = n1; // integer "widening"
//
//    var n3: f16 = 42.0;
//    var n4: f32 = n3; // float "widening"
//
// 3. Single-item pointers to arrays coerce to slices and
//    many-item pointers.
//
//    const arr: [3]u8 = [3]u8{5, 6, 7};
//    const s: []const u8 = &arr;  // to slice
//    const p: [*]const u8 = &arr; // to many-item pointer
//
// 4. Single-item mutable pointers can coerce to single-item
//    pointers pointing to an array of length 1. (Interesting!)
//
//    var five: u8 = 5;
//    var a_five: *[1]u8 = &five;
//
// 5. Payload types and null coerce to optionals.
//
//    var num: u8 = 5;
//    var maybe_num: ?u8 = num; // payload type
//    maybe_num = null;         // null
//
// 6. Payload types and errors coerce to error unions.
//
//    const MyError = error{Argh};
//    var char: u8 = 'x';
//    var char_or_die: MyError!u8 = char; // payload type
//    char_or_die = MyError.Argh;         // error
//
// 7. 'undefined' coerces to any type (or it wouldn't work!)
//
// 8. Compile-time numbers coerce to compatible types.
//
//    Just about every single exercise program has had an example
//    of this, but a full and proper explanation is coming your
//    way soon in the third-eye-opening subject of comptime.
//
// 9. Tagged unions coerce to the current tagged enum.
//
// 10. Enums coerce to a tagged union when that tagged field is a
//     zero-length type that has only one value (like void).
//
// 11. Zero-bit types (like void) can be coerced into single-item
//     pointers.
//
```

## Loop expressions
```
// But what value is returned from a loop if a break statement is
// never reached? We need a default expression. Thankfully, Zig
// loops also have 'else' clauses! As you might have guessed, the
// 'else' clause is evaluated when: 1) a 'while' condition becomes
// false or 2) a 'for' loop runs out of items.
//
//     const two: u8 = while (true) break 2 else 0;         // 2
//     const three: u8 = for ([1]u8{1}) |f| break 3 else 0; // 3
```

## Labels
```
// As we've just learned, you can return a value using a break
// statement. Does that mean you can return a value from any
// labeled block? Yes it does!
//
//     const foo = make_five: {
//         const five = 1 + 1 + 1 + 1 + 1;
//         break :make_five five;
//     };

// Being able to return a value from an
// inner loop is sometimes so handy, it almost feels like cheating
// (and can help you avoid creating a lot of temporary variables).
//
//     const bar: u8 = two_loop: while (true) {
//         while (true) {
//             break :two_loop 2;
//         }
//     } else 0;

// Finally, you can also use block labels with the 'continue'
// statement:
//
//     my_while: while (true) {
//         continue :my_while;
//     }
```