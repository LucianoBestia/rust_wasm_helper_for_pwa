[comment]: # (lmake_md_to_doc_comments segment start A)

# rust_wasm_png_resize_for_pwa

[comment]: # (lmake_cargo_toml_to_md start)

**creates a bunch of pngs for PWA icons**  
***[repo](https://github.com/LucianoBestia/rust_wasm_png_resize_for_pwa_game); version: 2020.1217.1220  date: 2020-12-17 authors: Luciano Bestia***  

[comment]: # (lmake_cargo_toml_to_md end)

[comment]: # (lmake_lines_of_code start)
[![Lines in Rust code](https://img.shields.io/badge/Lines_in_Rust-764-green.svg)](https://github.com/LucianoBestia/rust_wasm_png_resize_for_pwa/)
[![Lines in Doc comments](https://img.shields.io/badge/Lines_in_Doc_comments-32-blue.svg)](https://github.com/LucianoBestia/rust_wasm_png_resize_for_pwa/)
[![Lines in Comments](https://img.shields.io/badge/Lines_in_comments-61-purple.svg)](https://github.com/LucianoBestia/rust_wasm_png_resize_for_pwa/)
[![Lines in examples](https://img.shields.io/badge/Lines_in_examples-0-yellow.svg)](https://github.com/LucianoBestia/rust_wasm_png_resize_for_pwa/)
[![Lines in tests](https://img.shields.io/badge/Lines_in_tests-0-orange.svg)](https://github.com/LucianoBestia/rust_wasm_png_resize_for_pwa/)

[comment]: # (lmake_lines_of_code end)

[![Licence](https://img.shields.io/badge/license-MIT-blue.svg)](https://github.com/LucianoBestia/rust_wasm_png_resize_for_pwa/blob/master/LICENSE) [![Rust](https://github.com/LucianoBestia/rust_wasm_png_resize_for_pwa/workflows/RustAction/badge.svg)](https://github.com/LucianoBestia/rust_wasm_png_resize_for_pwa/)

## Try it

<https://bestia.dev/rust_wasm_png_resize_for_pwa/>

## how many icons?

PWA icons are totally confusing. There is no hard rule what to do and how many icons to have. Apple invented their system, google invented something different. Who is microsoft?  
And there are much too much resizing of the same image to do it manually.  
I think this is a nice candidate for an automation utility.  

I will start with one png file at least 512x512 px. This utility will then make all other pngs with different sizes. Also the manifest.json and html header will be prepared.  
I will use the info I found as of today. Even so there are different names and implementations. This will probably change in the future. I will try to update it later.   
 
But I don't want to make it a CLI, because it must be installed (be afraid of unknown programs), has complete access to all my system and it works only on one OS.  
I want to make a PWA utility: trustworthy, no-problem installation, cross-platform. But the downside is that it must be run manually. I think it is not possible to call it from a shell script. This is not a problem because changing the app icon image is important and should be done in edit-time when the programmer is still conscious.   
All the processing is done locally inside the browser. No server involved at all. No data is transferred to the server. Zero. Zilch. Nada.  
Let see where it takes us.  

## Select a file

PWA does not have access to local files. The first step is to `select` the original big png file manually.  

## resize the png

The crate [image](https://crates.io/crates/image) decodes, resizes and encodes the png file.

## zip

The crate [zip](https://crates.io/crates/zip) adds all files in one single zip file for easy downloading

## download

The newly created zip is then available to download from the browse. It is still data inside the browser. 
After unzipping, copy the png files into `icons/` folder.

## Conclusion

The wasm file is 1,1Mb. I mean it is not bad if you think that it includes the image and zip libraries. Maybe this 2 libraries could be smaller? Or maybe more selective what to include? I use a very small part of them. I don't know. It is good enough.  
Doing things in wasm is complicated because of the conversion between javascript objects and rust objects. There is so much to learn about all this conversions: js_sys::Uint8Array, web_sys::Element, HtmlElement, HtmlInputElement, HtmlAnchorElement, Blob, File, FileList, FileReader,...

In cargo.toml is important to add features to [dependencies.web-sys] whenever we need some new types from web_sys. That is not comfortable. Could be better with some special tool?

Another difficulty was how to reserve a big memory space for the zip.
First I tried with an array [0u8; 65_536]. It was too small. But the maximum size I could use was 524_288. I suppose arrays are on the stack and it is limited.
So I then used vec![0u8; 2_097_152]; with no errors. I suppose it goes into the heap memory, that is bigger. Just one single big allocation.
