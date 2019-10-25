# Snippets

A couple of snippets for things I have to do a lot but forget how every once in a while

## Using `.map` on a Hash

```ruby
{ a: 'a', b: 'b' }.map { |k, str| [k, "%#{str}%"] }.to_h
```



---



## Convert an Array of Hashes to a Hash

```ruby
# I have an array of hashes
array = [ {id: 1, name: "test"} ]

# I want to turn it to a hash, where the key is `id`
array.map { |h| [h[:id], h] }.to_h
#=> { 1 => {:id => 1, :name => "test"} } 
```

