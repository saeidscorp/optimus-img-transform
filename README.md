# optimus-img-transform [![Build Status](https://secure.travis-ci.org/magnars/optimus-img-transform.png)](http://travis-ci.org/magnars/optimus-img-transform)

An [Optimus](http://github.com/magnars/optimus) image transformation middleware.

## Install

- Add `[optimus-img-transform "0.2.0"]` to `:dependencies` in your `project.clj`.
- It requires Optimus version minimum `0.14.1`.
- It requires Java 7

## Usage

Let's look at an example:

```clj
(ns example
  (:require [optimus-img-transform.core :refer [transform-images]]))

(transform-images assets {:regexp #"/photos/.*\.jpg"
                          :quality 0.7
                          :width 290
                          :progressive true})
```

This takes a list of assets and transform all that match the
`:regexp`. They're resized to 290 width (maintaining proportions), and
saved with quality 0.7 in progressive rendering mode.

You can also specify:

- `:height` alone, or together with `:width`
- `:scale` which will resize proportional to the scale.
- `:tmp-dir` customizes the temporary location for cached images.
- `:prefix` to create new images on a prefixed path, instead of replacing.
- `:crop` to crop the image at `{:offset [x y], :size [w h]}`

The only mandatory params are `:regexp` and `:quality`.

Here's another example:

```clj
(transform-images assets {:regexp #"/photos/.*\.jpg"
                          :quality 0.3
                          :scale 2
                          :prefix "2x/"
                          :progressive true})
```

This sets up the neat trick of
[Retina Revolution](http://www.netvlies.nl/blog/design-interactie/retina-revolution)
to serve retina-ready images with no increase in file size.

Take an image `/photos/optimus.jpg` that's 290x180 in size. After
this, there's also an image `/photos/2x/optimus.jpg` that's at 580x360
with a low jpg quality.

## How do I use it with Optimus?

Just add it to your asset middleware stack. Let's say you want to use
all the optimizations that come with Optimus:

```clj
(ns example
  (:require [optimus.optimizations :as optimizations]
            [optimus-img-transform.core :refer [transform-images]]))

(defn optimize [assets options]
  (-> assets
      (transform-images {:regexp #"/photos/.*\.jpg"
                         :quality 0.3
                         :scale 2
                         :progressive true})
      (optimizations/all options)))
```

And plug that optimization function into `optimus.core/wrap`.

## Can I create multiple versions of the same image?

Yes, just use `:prefix` to differentiate them.

One word of warning tho: `transform-images` won't touch images that it
has already transformed, to avoid stacking jpg-compressions on top of
each other. So if you want to both create multiple versions of the
same image *and* transform the image in-place, do the in-place
transformation last.

## License

Copyright © 2014 Magnar Sveen

Distributed under the Eclipse Public License either version 1.0 or (at
your option) any later version.
