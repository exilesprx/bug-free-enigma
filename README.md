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

## Comptime
```
// ALL numeric literals in Zig are of type comptime_int or
// comptime_float. They are of arbitrary size (as big or
// little as you need).
//
// Notice how we don't have to specify a size like "u8",
// "i32", or "f64" when we assign identifiers immutably with
// "const".
//
// When we use these identifiers in our program, the VALUES
// are inserted at compile time into the executable code. The
// IDENTIFIERS "const_int" and "const_float" don't exist in
// our compiled application!
//
// const const_int = 12345;
// const const_float = 987.654;
//
// But something changes when we assign the exact same values
// to identifiers mutably with "var".

// When placed before a variable declaration, 'comptime'
// guarantees that every usage of that variable will be performed
// at compile time.
//
// As a simple example, compare these two statements:
//
//    var bar1 = 5;            // ERROR!
//    comptime var bar2 = 5;   // OKAY!
//
// The first one gives us an error because Zig assumes mutable
// identifiers (declared with 'var') will be used at runtime and
// we have not assigned a runtime type (like u8 or f32). Trying
// to use a comptime_int of undetermined size at runtime is
// a MEMORY CRIME and you are UNDER ARREST.

//
// You can also put 'comptime' before a function parameter to
// enforce that the argument passed to the function must be known
// at compile time. We've actually been using a function like
// this the entire time, std.debug.print():
//
//     fn print(comptime fmt: []const u8, args: anytype) void
//

//
// One of the more common uses of 'comptime' function parameters is
// passing a type to a function:
//
//     fn foo(comptime MyType: type) void { ... }
//
// In fact, types are ONLY available at compile time, so the
// 'comptime' keyword is required here.
//

//
// Being able to pass types to functions at compile time lets us
// generate code that works with multiple types. But it doesn't
// help us pass VALUES of different types to a function.
//
// For that, we have the 'anytype' placeholder, which tells Zig
// to infer the actual type of a parameter at compile time.
//
//     fn foo(thing: anytype) void { ... }
//
// Then we can use builtins such as @TypeOf(), @typeInfo(),
// @typeName(), @hasDecl(), and @hasField() to determine more
// about the type that has been passed in. All of this logic will
// be performed entirely at compile time.

// methods declared in namespaces is also known as "decls"

//
// An 'inline for' is performed at compile time, allowing you to
// programatically loop through a series of items in situations
// like those mentioned above where a regular runtime 'for' loop
// wouldn't be allowed:
//
//     inline for (.{ u8, u16, u32, u64 }) |T| {
//         print("{} ", .{@typeInfo(T).Int.bits});
//     }
//
// In the above example, we're looping over a list of types,
// which are available only at compile time.

//
// There is also an 'inline while'. Just like 'inline for', it
// loops at compile time, allowing you to do all sorts of
// interesting things not possible at runtime.

//
// As a matter of fact, you can put 'comptime' in front of any
// expression to force it to be run at compile time.
//
// Execute a function:
//
//     comptime llama();
//
// Get a value:
//
//     bar = comptime baz();
//
// Execute a whole block:
//
//     comptime {
//         bar = baz + biff();
//         llama(bar);
//     }
//
// Get a value from a block:
//
//     var llama = comptime bar: {
//         const baz = biff() + bonk();
//         break :bar baz;
//     }

// In addition to knowing when to use the 'comptime' keyword,
// it's also good to know when you DON'T need it.
//
// The following contexts are already IMPLICITLY evaluated at
// compile time, and adding the 'comptime' keyword would be
// superfluous, redundant, and smelly:
//
//    * The container-level scope (outside of any function in a source file)
//    * Type declarations of:
//        * Variables
//        * Functions (types of parameters and return values)
//        * Structs
//        * Unions
//        * Enums
//    * The test expressions in inline for and while loops
//    * An expression passed to the @cImport() builtin
//
// Work with Zig for a while, and you'll start to develop an
// intuition for these contexts. Let's work on that now.
```

## Sentinels
```
// A sentinel value indicates the end of data. Let's imagine a
// sequence of lowercase letters where uppercase 'S' is the
// sentinel, indicating the end of the sequence:
//
//     abcdefS
//
// If our sequence also allows for uppercase letters, 'S' would
// make a terrible sentinel since it could no longer be a regular
// value in the sequence:
//
//     abcdQRST
//          ^-- Oops! The last letter in the sequence is R!
//
// Zig supports sentinel-terminated arrays, slices, and pointers:
//
//     const a: [4:0]u32       =  [4:0]u32{1, 2, 3, 4};
//     const b: [:0]const u32  = &[4:0]u32{1, 2, 3, 4};
//     const c: [*:0]const u32 = &[4:0]u32{1, 2, 3, 4};
//
// Array 'a' stores 5 u32 values, the last of which is 0.
// However the compiler takes care of this housekeeping detail
// for you. You can treat 'a' as a normal array with just 4
// items.
//
// Slice 'b' is only allowed to point to zero-terminated arrays
// but otherwise works just like a normal slice.
//
// Pointer 'c' is exactly like the many-item pointers we learned
// about in exercise 054, but it is guaranteed to end in 0.
// Because of this guarantee, we can safely find the end of this
// many-item pointer without knowing its length. (We CAN'T do

//
// Are you ready for the THE TRUTH about Zig string literals?
//
// Here it is:
//
//     @TypeOf("foo") == *const [3:0]u8
//
// Which means a string literal is a "constant pointer to a
// zero-terminated (null-terminated) fixed-size array of u8".
//
// Now you know. You've earned it. Welcome to the secret club!

//
// Why do we bother using a zero/null sentinel to terminate
// strings in Zig when we already have a known length?
//
// Versatility! Zig strings are compatible with C strings (which
// are null-terminated) AND can be coerced to a variety of other
// Zig types:
//
//     const a: [5]u8 = "array".*;
//     const b: *const [16]u8 = "pointer to array";
//     const c: []const u8 = "slice";
//     const d: [:0]const u8 = "slice with sentinel";
//     const e: [*:0]const u8 = "many-item pointer with sentinel";
//     const f: [*]const u8 = "many-item pointer";
//
// All but 'f' may be printed. (A many-item pointer without a
// sentinel is not safe to print because we don't know where it
// ends!)

//
// We were able to get a printable string out of a many-item
// pointer by using a slice to assert a specific length.
//
// But can we ever GO BACK to a sentinel-terminated pointer
// after we've "lost" the sentinel in a coercion?
//
// Yes, we can. Zig's @ptrCast() builtin can do this. Check out
// the signature:
//
//     @ptrCast(value: anytype) anytype
```
## Quoted identifiers
```
//
// Sometimes you need to create an identifier that will not, for
// whatever reason, play by the naming rules:
//
//     const 55_cows: i32 = 55; // ILLEGAL: starts with a number
//     const isn't true: bool = false; // ILLEGAL: what even?!
//
// If you try to create either of these under normal
// circumstances, a special Program Identifier Syntax Security
// Team (PISST) will come to your house and take you away.
//
// Thankfully, Zig has a way to sneak these wacky identifiers
// past the authorities: the @"" identifier quoting syntax.
//
//     @"foo"
//
// Please help us safely smuggle these fugitive identifiers into
// our program:
```

## Structs
```
//
// Struct types are always "anonymous" until we give them a name:
//
//     struct {};
//
// So far, we've been giving struct types a name like so:
//
//     const Foo = struct {};
//
// * The value of @typeName(Foo) is "<filename>.Foo".
//
// A struct is also given a name when you return it from a
// function:
//
//     fn Bar() type {
//         return struct {};
//     }
//
//     const MyBar = Bar();  // store the struct type
//     const bar = Bar() {}; // create instance of the struct
//
// * The value of @typeName(Bar()) is "Bar()".
// * The value of @typeName(MyBar) is "Bar()".
// * The value of @typeName(@TypeOf(bar)) is "Bar()".
//
// You can also have completely anonymous structs. The value
// of @typeName(struct {}) is "struct:<position in source>".
//

//
// An anonymous struct value LITERAL (not to be confused with a
// struct TYPE) uses '.{}' syntax:
//
//     .{
//          .center_x = 15,
//          .center_y = 12,
//          .radius = 6,
//     }
//
// These literals are always evaluated entirely at compile-time.

//
// You can even create anonymous struct literals without field
// names:
//
//     .{
//         false,
//         @as(u32, 15),
//         @as(f64, 67.12)
//     }
//
// We call these "tuples", which is a term used by many
// programming languages for a data type with fields referenced
// by index order rather than name. To make this possible, the Zig
// compiler automatically assigns numeric field names 0, 1, 2,
// etc. to the struct.
//
// Since bare numbers are not legal identifiers (foo.0 is a
// syntax error), we have to quote them with the @"" syntax.
// Example:
//
//     const foo = .{ true, false };
//
//     print("{} {}\n", .{foo.@"0", foo.@"1"});
//

// Anonymous struct literal syntax can also be used to compose an
// "anonymous list" with an array type destination:
//
//     const foo: [3]u32 = .{10, 20, 30};
//
// Otherwise it's a "tuple":
//
//     const bar = .{10, 20, 30};
//
// The only difference is the destination type.
//
```

## Async - regressed and not present in the current version as of writing this
```
//
// Six Facts:
//
// 1. The memory space allocated to your program for the
// invocation of a function and all of its data is called a
// "stack frame".
//
// 2. The 'return' keyword "pops" the current function
// invocation's frame off of the stack (it is no longer needed)
// and returns control to the place where the function was
// called.
//
//     fn foo() void {
//         return; // Pop the frame and return control
//     }
//
// 3. Like 'return', the 'suspend' keyword returns control to the
// place where the function was called BUT the function
// invocation's frame remains so that it can regain control again
// at a later time. Functions which do this are "async"
// functions.
//
//     fn fooThatSuspends() void {
//         suspend {} // return control, but leave the frame alone
//     }
//
// 4. To call any function in async context and get a reference
// to its frame for later use, use the 'async' keyword:
//
//     var foo_frame = async fooThatSuspends();
//
// 5. If you call an async function without the 'async' keyword,
// the function FROM WHICH you called the async function itself
// becomes async! In this example, the bar() function is now
// async because it calls fooThatSuspends(), which is async.
//
//     fn bar() void {
//         fooThatSuspends();
//     }
//
// 6. The main() function cannot be async!
//
// Given facts 3 and 4, how do we fix this program (broken by facts
// 5 and 6)?
//

//
// So, 'suspend' returns control to the place from which it was
// called (the "call site"). How do we give control back to the
// suspended function?
//
// For that, we have a new keyword called 'resume' which takes an
// async function invocation's frame and returns control to it.
//
//     fn fooThatSuspends() void {
//         suspend {}
//     }
//
//     var foo_frame = async fooThatSuspends();
//     resume foo_frame;
//

//
// Because they can suspend and resume, async Zig functions are
// an example of a more general programming concept called
// "coroutines". One of the neat things about Zig async functions
// is that they retain their state as they are suspended and
// resumed.
//

//
// The 'await' keyword waits for an async function to complete
// and then captures its return value.
//
//     fn foo() u32 {
//         return 5;
//     }
//
//    var foo_frame = async foo(); // invoke and get frame
//    var value = await foo_frame; // await result using frame
//

//
// Remember how a function with 'suspend' is async and calling an
// async function without the 'async' keyword makes the CALLING
// function async?
//
//     fn fooThatMightSuspend(maybe: bool) void {
//         if (maybe) suspend {}
//     }
//
//     fn bar() void {
//         fooThatMightSuspend(true); // Now bar() is async!
//     }
//
// But if you KNOW the function won't suspend, you can make a
// promise to the compiler with the 'nosuspend' keyword:
//
//     fn bar() void {
//         nosuspend fooThatMightSuspend(false);
//     }
//
// If the function does suspend and YOUR PROMISE TO THE COMPILER
// IS BROKEN, the program will panic at runtime, which is
// probably better than you deserve, you oathbreaker! >:-(
//
```
