## Draw fractal dispatching work to a thread pool

[![threadpool-badge]][threadpool] [![num-badge]][num] [![num_cpus-badge]][num_cpus] [![image-badge]][image] [![cat-concurrency-badge]][cat-concurrency][![cat-science-badge]][cat-science][![cat-rendering-badge]][cat-rendering]

This example generates an image by drawing a fractal from the [Julia set]
with a thread pool for distributed computation.

<a href="https://cloud.githubusercontent.com/assets/221000/26546700/9be34e80-446b-11e7-81dc-dd9871614ea1.png"><img src="https://cloud.githubusercontent.com/assets/221000/26546700/9be34e80-446b-11e7-81dc-dd9871614ea1.png" width="150" /></a>

Allocate memory for output image of given width and height with [`ImageBuffer::new`].
[`Rgb::from_channels`] calculates RGB pixel values.
Create [`ThreadPool`] with thread count equal to number of cores with [`num_cpus::get`].
[`ThreadPool::execute`] receives each pixel as a separate job.

[`mpsc::channel`] receives the jobs and [`Receiver::recv`] retrieves them.
[`ImageBuffer::put_pixel`] uses the data to set the pixel color.
[`ImageBuffer::save`] writes the image to `output.png`.

```rust,edition2024,no_run
use image::{Rgb, RgbImage};
use num::complex::Complex;
use std::sync::mpsc::{channel};
use threadpool::ThreadPool;

fn generate_color_linear_gradient(t: f32) -> [u8; 3] {
    let t = t.clamp(0.0, 1.0);

    let colors: &[[u8; 3]] = &[
        [0, 0, 0],       // Black
        [0, 0, 128],     // Dark Blue
        [0, 0, 255],     // Blue
        [0, 128, 255],   // Sky Blue
        [255, 255, 255], // White
        [255, 128, 0],   // Orange
        [255, 0, 0],     // Red
        [128, 0, 0],     // Dark Red
        [0, 0, 0],       // Black
    ];

    let num_colors = colors.len() - 1;

    let scaled_t = t * num_colors as f32;

    let idx1 = scaled_t.floor() as usize;
    let idx2 = (scaled_t.ceil() as usize).min(num_colors); 

    let blend_factor = scaled_t - idx1 as f32;

    let c1 = colors[idx1];
    let c2 = colors[idx2];

    [
        (c1[0] as f32 * (1.0 - blend_factor) + c2[0] as f32 * blend_factor) as u8,
        (c1[1] as f32 * (1.0 - blend_factor) + c2[1] as f32 * blend_factor) as u8,
        (c1[2] as f32 * (1.0 - blend_factor) + c2[2] as f32 * blend_factor) as u8,
    ]
}

fn juila(
    x: u32,
    y: u32,
    width: u32,
    height: u32,
    zoom: f32,
    c: Complex<f32>,
    iterations: u32,
    shift: Complex<f32>,
) -> f32 {
    let half_width = width as f32 * 0.5;
    let half_height = height as f32 * 0.5;
    let aspect_ratio = width as f32 / height as f32;

    let mut z = Complex::new(
        (x as f32 - half_width) / (zoom * half_width) * aspect_ratio,
        (y as f32 - half_height) / (zoom * half_height),
    ) + shift;

    let mut i = 0;

    while z.norm() < 4.0 && i <iterations {
        let tmp = z.re*z.re - z.im * z.im  + c.re;
        z.im = 2.0 * z.re * z.im + c.im;
        z.re = tmp;
        i += 1;
    }
    let norm = i as f32 / iterations as f32;
    norm
}

fn main() {
    let width: u32 = 1920;
    let height: u32 = 1920;
    let zoom = 1.0;
    let mut img = RgbImage::new(width, height);

    let iterations = 255;
    let c = Complex::new(-0.8, 0.156);
    let shift = Complex::new(0.0, 0.0);

    let pool = ThreadPool::new(num_cpus::get());
    let (tx, rx) = channel();

    for x in 0..width {
        let tx = tx.clone();
        pool.execute(move || {
            for y in 0..height {
                let i = juila(x, y, width, height, zoom, c, iterations, shift);
                let pixel = generate_color_linear_gradient(i);
                tx.send((x, y, pixel)).expect("Could not send data!");
            }
        });
    }

    for _ in 0..(width * height) {
        let (x, y, pixel) = rx.recv().expect("Could not receive data!");
        img.put_pixel(x, y, Rgb(pixel));
    }

    let _ = img.save("output.png").expect("Failed to save image");
}

```

[`ImageBuffer::new`]: https://docs.rs/image/*/image/struct.ImageBuffer.html#method.new
[`ImageBuffer::put_pixel`]: https://docs.rs/image/*/image/struct.ImageBuffer.html#method.put_pixel
[`ImageBuffer::save`]: https://docs.rs/image/*/image/struct.ImageBuffer.html#method.save
[`mpsc::channel`]: https://doc.rust-lang.org/std/sync/mpsc/fn.channel.html
[`num_cpus::get`]: https://docs.rs/num_cpus/*/num_cpus/fn.get.html
[`Receiver::recv`]: https://doc.rust-lang.org/std/sync/mpsc/struct.Receiver.html#method.recv
[`Rgb::from_channels`]: https://docs.rs/image/*/image/struct.Rgb.html#method.from_channels
[`ThreadPool`]: https://docs.rs/threadpool/*/threadpool/struct.ThreadPool.html
[`ThreadPool::execute`]: https://docs.rs/threadpool/*/threadpool/struct.ThreadPool.html#method.execute

[Julia set]: https://en.wikipedia.org/wiki/Julia_set
