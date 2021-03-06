---
layout: representable
---

# Representable: Performance

Since Representable 2.2 we provide the feature `Representable::Cached` which boost up rendering and parsing speed by about 50%.

Instead of unnecessarily creating (sometimes thousands of) nested representers, bindings, and associated instances for every represented object, this is now cached. By making `Binding` instances stateless we can reuse it.

[Example]
Now, only one binding instance is used to render/parse the entire collection.


## Usage

As per Representable 2.2 this feature is still experimental. Nevertheless, we recommend using it. Note that caching **only works with decorators**.


	class AlbumRepresenter < Representable::Decorator
	  include Representable::Hash
	  feature Representable::Cached

	  property :name

	  collection :songs do
	    property :title
	  end
	end


Inserting the module on the top-level representer via `feature Representable::Cached` will activate caching for all representers in the graph.

[speedup when rendering/parsing]

## Reusing the top-level Representer

It is also possible to cache the top-level representer and reuse it, e.g. for multiple requests. Of course, doing so is your job. However, once the decorator has been initialized the first time, it can be reused using `#update!`. Again, doing so is up to you.

[roar-rails support]

	decorator = AlbumRepresenter.new(album)

	decorator.to_json # first request.

	decorator.update!(album) # second request.
	decorator.to_json


Since the decorator and its bindings are stateless, you only need to update the represented object. When rendering or parsing, all changing runtime data is then passed the the respective sub-systems using method arguments. No state is held.

## Pitfalls

Using `Cached` currently hooks into the `Deserializer:#prepare` method. If you use `:prepare` to instantiate different representers per iteration, you might have problems with caching. I'll update this soon.

[Graph of caching hierarchy]
