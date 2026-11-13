# Camera

The Arche phone's camera app, written in [Arche](https://github.com/arche-family/arche-lang): a WebAssembly component of the Arche phone's world, which the phone has from its first start. That world is Nimadir's mini-app world and what a phone adds to it, here the phone's camera; its WIT is in `wit/` (from arche-os's `phone/wit`), and `world` names it. Nimadir's own shell, which has none of it, does not run it.

It works like Android's camera app:

- What the camera sees, live, filling the width, on black.
- A round shutter button under it takes a photo. The phone keeps it in the shared Pictures folder, named by when it was taken (`IMG_20261110_120000.png`), and the app says its name once it is kept.
- Photos opens the gallery.
- On a phone with a camera on its front, a button beside the shutter turns to it, and back.
- Video records what the camera sees, 30 frames a second, into the shared Movies folder (`VID_20261110_120000.avi`, Motion JPEG in AVI) until Stop.
- A phone without a camera says so.

The camera's view is an image the phone keeps up to date frame by frame (the phone world's `camera.view`), drawn in an `img`; the app asks for a frame every 100 ms while it is drawn. A photo is `camera.take`, and `camera.last` names it. On a phone with a camera on its front (`camera.has-front`), a button by the shutter turns the view and photos to it and back (`camera.use-front`). A video is `camera.start-video` and `camera.stop-video`, and `camera.recording` says whether one is being recorded; while it is, the app asks for a frame every 33 ms. The phone gives these to an app whose manifest needs `camera`. It is an app of the page: its taps come as click events.

## Build

With [arche-lang](https://github.com/arche-family/arche-lang) checked out next to this repository and built (`make -C stage0 && make -C compiler && make -C wasm`):

```sh
../arche-lang/wasm/nimadir-app.sh . camera.component.wasm
```

`manifest` is what the phone's repository packages with the component. It gives the app's name, version and title, and what it may do: use the camera (`camera`).

## Test

`headless` holds the taps of a headless run and the functions the phone's world adds, as a phone without a camera answers them, but with one on its front: the shutter tapped, its call made, the front camera's button tapped, Video tapped, and Photos asked for. `expected.txt` holds what arche-wasm's interpreter said, as Nimadir's shell does not run the phone's world; `normalize.sed` takes out where the component was built, which the shell names when the app asks to open another. `app-check.sh` runs the app with arche-wasm, by the interpreter and by the JIT, and compares what it says with `expected.txt`. CI runs it on every push. The cameras themselves, the view, a photo taken and the turn to the front camera, are tried on the phone, with the kernel's virtual camera, by arche-os's `tests/phone.sh`.

```sh
../arche-lang/wasm/app-check.sh .
NIMADIR=~/nimadir ../arche-lang/wasm/app-expected.sh .   # expected.txt again, from arche-wasm
```

## License

MIT or Apache-2.0, at your option ([LICENSE](LICENSE)).
