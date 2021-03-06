# Introducing `?`

Sometimes we just want the simplicity of `unwrap` without the possibility of 
a `panic`. Until now, `unwrap` has forced us to nest deeper and deeper when 
what we really wanted was to get the variable *out*. This is exactly the purpose of `?`. 

Upon finding an `Err`, there are two valid actions to take:

1. `panic!` which we already decided to try to avoid if possible
2. `return` because an `Err` means it cannot be handled

`?` is *almost*[^1] exactly equivalent to an `unwrap` which `return`s 
instead of `panic`s on `Err`s. Let's see how we can simplify the earlier 
example that used combinators:

```rust,editable
// Use `String` as our error type
type Result<T> = std::result::Result<T, String>;

fn double_first(vec: Vec<&str>) -> Result<i32> {
    let first = vec.first()
        .ok_or("Please use a vector with at least one element.".to_owned())?;
    
    let value = first.parse::<i32>()
        .map_err(|e| e.to_string())?;
    
    Ok(2 * value)
}

fn print(result: Result<i32>) {
    match result {
        Ok(n)  => println!("The first doubled is {}", n),
        Err(e) => println!("Error: {}", e),
    }
}

fn main() {
    let empty = vec![];
    let strings = vec!["tofu", "93", "18"];

    print(double_first(empty));
    print(double_first(strings));
}
```

Note that up until now, we've been using `String`s as errors. However, they 
are somewhat limiting as an error type. In the next section, we'll learn how 
to make more structured and informative errors by defining their types. 


## The `try!` macro

Before there was `?`, the same functionality was achieved with the `try!` macro.
The `?` operator is now recommended, but you may still find `try!` when looking
at older code. The same `double_first` function from the previous example 
would look like this using `try!`:

```rust,ignore
fn double_first(vec: Vec<&str>) -> Result<i32> {
    let first = try!(vec.first()
        .ok_or("Please use a vector with at least one element.".to_owned()));
    
    let value = try!(first.parse::<i32>()
        .map_err(|e| e.to_string()));
    
    Ok(2 * value)
}
```


[^1]: See [re-enter ?][re_enter_?] for more details.

### See also:

[`Result`][result] and [`io::Result`][io_result]

[result]: https://doc.rust-lang.org/std/result/enum.Result.html
[io_result]: https://doc.rust-lang.org/std/io/type.Result.html
[re_enter_?]: /error/reenter_question_mark.html
