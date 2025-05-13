# EVENTS

```js
export const deployButton = ({ label }) => {
	
	function handleClick(evt) {
		evt.preventDefault()
		alert('Site uploaded!')
	}

	return (
		<>
			<Label label={label} />
			<div className="preview-buttons">
				<a onClick={handleClick} href={`deploy-prod`}>
					<Button
						className="preview-button"
						buttonStyle="primary">Live
					</Button>
				</a>
				<a href="">
					<Button
						className="preview-button"
						buttonStyle="secondary">Test
					</Button>
				</a>
			</div>
		</>
	);
};
```



# HOOKS

## useState()

```js
const [state, setState] = useState(initialState) // returns an array with exactly two values:
// * The current state. During the first render, it will match the initialState you have passed.
// * The set function that lets you update the state to a different value and trigger a re-render.

const [color, setColor] = useState('')
```

* lets you add a state variable to your component

## useEffect()

* setup function
* runs when component is added to the DOM but after `useState()`

```js
useEffect(setup, dependencies?)
          
useEffect(() => {
		async function getColorFromPayload() {
			// Retrieve preferences on component mount
			// This will only be run one time, because the `getPreference` method never changes
			const color = await getPreference('color');
			console.log('got color from preferences: ', color)
			setColor(color)
		}

		getColorFromPayload()

	}, [getPreference]); // getPreference is a function to connect with payload and retrieve data
```

