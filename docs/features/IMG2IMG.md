---
title: Image-to-Image
---

# :material-image-multiple: Image-to-Image

InvokeAI provides an "img2img" feature that lets you seed your
creations with an initial drawing or photo. This is a really cool
feature that tells stable diffusion to build the prompt on top of the
image you provide, preserving the original's basic shape and layout.

For a walkthrough of using Image-to-Image in the Web UI, see [InvokeAI
Web Server](./WEB.md#image-to-image).

The main difference between `img2img` and `prompt2img` is the starting point.
While `prompt2img` always starts with pure gaussian noise and progressively
refines it over the requested number of steps, `img2img` skips some of these
earlier steps (how many it skips is indirectly controlled by the `--strength`
parameter), and uses instead your initial image mixed with gaussian noise as the
starting image.

**Let's start** by thinking about vanilla `prompt2img`, just generating an image
from a prompt. If the step count is 10, then the "latent space" (Stable
Diffusion's internal representation of the image) for the prompt "fire" with
seed `1592514025` develops something like this:

!!! example ""

    <figure markdown>
    ![latent steps](../assets/img2img/000019.steps.png){ width=720 }
    </figure>

Put simply: starting from a frame of fuzz/static, SD finds details in each frame
that it thinks look like "fire" and brings them a little bit more into focus,
gradually scrubbing out the fuzz until a clear image remains.

**When you use `img2img`** some of the earlier steps are cut, and instead an
initial image of your choice is used. But because of how the maths behind Stable
Diffusion works, this image needs to be mixed with just the right amount of
noise (fuzz/static) for where it is being inserted. This is where the strength
parameter comes in. Depending on the set strength, your image will be inserted
into the sequence at the appropriate point, with just the right amount of noise.

### A concrete example

!!! example "I want SD to draw a fire based on this hand-drawn image"

    ![drawing of a fireplace](../assets/img2img/fire-drawing.png){ align=left }

    Let's only do 10 steps, to make it easier to see what's happening. If strength
    is `0.7`, this is what the internal steps the algorithm has to take will look
    like:

    <figure markdown>
    ![gravity32](../assets/img2img/000032.steps.gravity.png)
    </figure>

    With strength `0.4`, the steps look more like this:

    <figure markdown>
    ![gravity30](../assets/img2img/000030.steps.gravity.png)
    </figure>

Notice how much more fuzzy the starting image is for strength `0.7` compared to
`0.4`, and notice also how much longer the sequence is with `0.7`:

|                             | strength = 0.7                                                | strength = 0.4                                                |
| --------------------------- | ------------------------------------------------------------- | ------------------------------------------------------------- |
| initial image that SD sees  | ![step-0](../assets/img2img/000032.step-0.png)                | ![step-0](../assets/img2img/000030.step-0.png)                |
| steps argument to `invoke>` | `-S10`                                                        | `-S10`                                                        |
| steps actually taken        | `7`                                                           | `4`                                                           |
| latent space at each step   | ![gravity32](../assets/img2img/000032.steps.gravity.png)      | ![gravity30](../assets/img2img/000030.steps.gravity.png)      |
| output                      | ![000032.1592514025](../assets/img2img/000032.1592514025.png) | ![000030.1592514025](../assets/img2img/000030.1592514025.png) |

Both of the outputs look kind of like what I was thinking of. With the strength
higher, my input becomes more vague, _and_ Stable Diffusion has more steps to
refine its output. But it's not really making what I want, which is a picture of
cheery open fire. With the strength lower, my input is more clear, _but_ Stable
Diffusion has less chance to refine itself, so the result ends up inheriting all
the problems of my bad drawing.

If you want to try this out yourself, all of these are using a seed of
`1592514025` with a width/height of `384`, step count `10`, the
`k_lms` sampler, and the single-word prompt `"fire"`.

### Compensating for the reduced step count

After putting this guide together I was curious to see how the difference would
be if I increased the step count to compensate, so that SD could have the same
amount of steps to develop the image regardless of the strength. So I ran the
generation again using the same seed, but this time adapting the step count to
give each generation 20 steps.

Here's strength `0.4` (note step count `50`, which is `20 ÷ 0.4` to make sure SD
does `20` steps from my image):

<figure markdown>
![000035.1592514025](../assets/img2img/000035.1592514025.png)
</figure>

and here is strength `0.7` (note step count `30`, which is roughly `20 ÷ 0.7` to
make sure SD does `20` steps from my image):

<figure markdown>
![000046.1592514025](../assets/img2img/000046.1592514025.png)
</figure>

In both cases the image is nice and clean and "finished", but because at
strength `0.7` Stable Diffusion has been give so much more freedom to improve on
my badly-drawn flames, they've come out looking much better. You can really see
the difference when looking at the latent steps. There's more noise on the first
image with strength `0.7`:

<figure markdown>
![gravity46](../assets/img2img/000046.steps.gravity.png)
</figure>

than there is for strength `0.4`:

<figure markdown>
![gravity35](../assets/img2img/000035.steps.gravity.png)
</figure>

and that extra noise gives the algorithm more choices when it is evaluating how
to denoise any particular pixel in the image.

Unfortunately, it seems that `img2img` is very sensitive to the step count.
Here's strength `0.7` with a step count of `29` (SD did 19 steps from my image):

<figure markdown>
![gravity45](../assets/img2img/000045.1592514025.png)
</figure>

By comparing the latents we can sort of see that something got interpreted
differently enough on the third or fourth step to lead to a rather different
interpretation of the flames.

<figure markdown>
![gravity46](../assets/img2img/000046.steps.gravity.png)
</figure>

<figure markdown>
![gravity45](../assets/img2img/000045.steps.gravity.png)
</figure>

This is the result of a difference in the de-noising "schedule" - basically the
noise has to be cleaned by a certain degree each step or the model won't
"converge" on the image properly (see
[stable diffusion blog](https://huggingface.co/blog/stable_diffusion) for more
about that). A different step count means a different schedule, which means
things get interpreted slightly differently at every step.
