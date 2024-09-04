---
layout: post
title: "1BRC as an idea to test myself with Rust"
date: 2024-09-04
category:
  - "Mortumm"
---

I did not compete or complete anything for the Advent of Code challenge, merely watched it unfold through the December of 2023.
But, it did spark an interest!
Shortly after New Years Eve, I came a accross a similar challenge: 1BRC by Gunnar Morling and thought that this could be the test case I was looking for. It being public, thoroughly documented and well known, I would have the chance to compare and learn from others implementations.

The 1BRC in itself is great and adjustable challenge to meet one's goals and needs.

Idea was to try out Rust and see what I can accomplish with the limited knowledge I had, and in the process acquint myself deeper in to the language.

### Generating a file to parse through

First part to work on, was a measurement generator. The information required for the dataset includes the ID (city) and its temperatures at a given moment in time. *Shouldn't be that hard?*
I was mistaken. There were couple of kinks to iron out.

```
const NUM_ROWS: usize = 1_000_000_000;
const NUM_STATIONS: usize = 10_000;

 fn main() {
     let file = File::create("measurements.txt").unwrap();
     let mut writer = BufWriter::new(file);
     let mut rng = rand::thread_rng();

     for _ in 0..NUM_ROWS {
         let station = format!("S{:04}", rng.gen_range(0..NUM_STATIONS));
         let temperature = rng.gen_range(-99.9..99.9);
         writeln!(writer, "{};{:.1}", station, temperature).unwrap();
     }

     writer.flush().unwrap();
 }
 ```
Setting up the number of rows and city ID's with the crate rng and their respective ranges.

Once I had a file containing the data to actually parse through, it was time to see how to approach the original problem: How to parse the generated information fast?

### Parsing the generated text file

Big O complexity was the starting place, when coming up with the implementation. What kind approach of complexity was best suited for a fast read and search? Array shouldn't be bad, for most scenarios but I was eager to try Hash Tables for this. As per the [rustlang doc](https://doc.rust-lang.org/stable/std/collections/struct.HashMap.html), it defaults to SipHash for now (per doc) and by its description "it is very competitive for medium sized keys". This sounds promising, *because performance*!

```
let results = Arc::new(Mutex::new(HashMap::<String, StationData>::new()));
```

To the point of reading the information from the generated file and organizing it (min & max), was done with the following:

```
while reader.stream_position().unwrap() < end {
    buffer.clear();
    if reader.read_line(&mut buffer).unwrap() == 0 {
        break;
    }
    let parts: Vec<&str> = buffer.trim().split(';').collect();
    if parts.len() == 2 {
        let station = parts[0].to_string();
        let temperature: f32 = parts[1].parse().unwrap();
        let entry = local_results.entry(station).or_insert(StationData {
            min: temperature,
            max: temperature,
            sum: temperature as f64,
            count: 1,
        });
        entry.min = entry.min.min(temperature);
        entry.max = entry.max.max(temperature);
        entry.sum += temperature as f64;
        entry.count += 1;
    }
}
```

Getting the temperature highs and lows, then the average.

```
let mut output_file = File::create("results.txt").expect("Failed to create output file");

for (station, data) in final_results.iter() {
    let output_line = format!(
        "{}: min={:.1}, max={:.1}, avg={:.1}\n",
        station,
        data.min,
        data.max,
        data.sum / data.count as f64
    );
```

Off to load the results to a txt file, and voíla.

### Testing and results

For tracking and measuring performance, I ran it automated and locally with cargo bench and by taking advantage of criterion crate.
In practice this meant 10x times through the 1,000,000 lines per benchmark, just for safetys sake. Total 10 times.
This could (Possibly?) be extended for the full 1,000,000,000 lines (1BRC version).
Local tests were done on my M2 Pro.

The average run time was 6,4s, variation range was from low 5 seconds to high 7 seconds (5,5 - 7,7).
From these I _could_ get graphs with gnuplot, but that remains to be done later, if ever. Time is of the essence, and currently the essence is in very short supply.

### Summary

All in all, a great experience! Like any sideproject or idea that actually carries or get carried to fruitition by my overly stimulated brain with constant distractions. And this was truly (On my metrics atleast) a completed and documented dive to something new and exciting.

There are already plenty of things that I could or would do differently, that is kind of the whole process of learning for me.

Thank you's go to the community and the people behind things like Advent of Code and 1BRC. Love em'!
