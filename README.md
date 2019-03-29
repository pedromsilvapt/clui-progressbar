# clui-progressbar
> Simple terminal progress bar, backed by clui-live

## Installation
```shell
npm install --save clui-progressbar
```

## Usage

You can call the `.setState()` method when updating more than just the progress, or simple set the handly `.percentage` property on the object.
```typescript
import { LiveProgressBar } from 'clui-progressbar';

const progressbar = new LiveProgressBar( { title: 'Long running task...' } );

for ( let i = 0; i < 100; i++ ) {
    progressbar.percentage = i;

    await sleepRandom();
}

for ( let i = 0; i < 100; i++ ) {
    progressbar.setState( {
        percentage: i,
        rightLabel: 'Some other stat (like speed)'
    } );

    await sleepRandom();
}

// The progressbar is a live area, so we can use any of the habitual methods on it, like closing
progressbar.close();
```
## API
The component `LiveProgressBar` extends the `LiveComponent`, which in turn extends the `LiveArea` class.

### interface ProgressBarState
```typescript
interface ProgressBarState {
    // Shown on the line above the progress bar
    title ?: string;
    // A value between 0 and 100. Values outside of this range are clamped.
    percentage : number;
    // Shown on the left side of the progressbar
    leftLabel ?: string;
    // Shown on the right side of the progressbar
    rightLabel ?: string;
}
```

### new LiveProgressBar( initialState ?: ProgressBarState )
Creates a LiveProgressBar. Optionaly accepts an initial state.

#### .progress : number
Is an alias to `.state.progress` when getting, and to `.setState( { progress: value } )` when setting.

#### .state : ProgressBarState
Holds the current state of the component. Should not be updated manually, as doing so won't not refresh the terminal

#### .maxWidth : number = Infinity
Allows to define a max width for the progress bar (including any labels it might have). By default it's value is infinity, which
means the progress bar tries to fit the entire terminal's width.

#### .hook() : this
Hooks this component to the global `LiveContainer`. Useful when there may be lines written to the terminal while the progress bar is showing, which would traditionally cause the output to become a mess (since the cursor would now be in a different position).
Calling this method monkey patches some methods (like `console.log`) to account for this type of sittuations.

#### .clear() : this
Can be called to clear the output of the progress bar from the screen. There is no need to manually call this method
when updating the progress bar. However it can come in handy if we don't want the progress bar to remain visible after it has finished (must be called before `.close()`).

#### .close() : this
Should be called when the progress bar is finished and won't be used any more, to release resources
