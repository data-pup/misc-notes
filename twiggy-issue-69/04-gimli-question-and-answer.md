After getting some of the initial work done on this, I ran into a little bit
of confusion regarding how to get the debug information found in `.debug_info`
out of the file object provided by the `object` crate. I reached out for
some advice on this, below is some of the information that I got back.

This is stored here mostly for my own reference going forward on this issue.

### Fetching Debug Information

There is a method that is used to select section data, given a specific name.
This method is documented here:

```
https://docs.rs/object/0.8.0/object/trait.Object.html#tymethod.section_data_by_name
```

So we will want to create a `object::File`, and then call
`file.section_data_by_name(".debug_info")`

From there, we can use the other advice previous given to step through the
entries in the section.

