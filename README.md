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
//
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
// If canFail() fails, foo will equal 6.
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