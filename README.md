# piquet-de-stream-overlay

## Requirements

- [NodeJS](https://nodejs.org/en/) 18.13 or greater

## Installation

```
npm install
```

## Contributing

- Run local dev server with `npm run dev`
- _Base HTML file : `src/app.html` (probably doesn't need modifications)_
- **Main overlay page** : `src/routes/+page.svelte` (it's just basic HTML, but [better](https://svelte.dev/tutorial/adding-data))
- Base CSS rules : `src/base.css`

Generate fake payment events :

```js
for (let i = 0; i < 5; i++) {
	await new Promise((res) => setTimeout(res, 1000));
	window.ping({ data: { name: `user${i}`, amount: 500 } });
}
```
