# Custom Scrollbar

Creates a custom scrollbar intended for use in specific cases where it is needed, eg tables where you dont want the default scrollbar.

## Basic usage

```html
<CustomScrollbar aria-controls="__nuxt" class="h-full top-0 right-0" />
```

## Props overview

#### CustomScrollbar's props overview

| Prop            | Description                                                               | Default value | Data type       |
| --------------- | ------------------------------------------------------------------------- | ------------- | --------------- |
| persistent      | Enables the scrollbar always, even though the target is scrollable        | false         | Boolean         |
| ariaControls    | Sets the first row of the table as the table header.                      | null          | String          |
| ariaValueNow    | Sets the first column of the table as the table header.                   | 0             | [String,Number] |
| ariaValuemin    | Sets the minimum value of the scrollbar as aria property `aria-valuemin`  | 0             | [String,Number] |
| ariaValuemax    | Sets the maximum value of the scrollbar as aria property `aria-valuemin`  | 100           | [String,Number] |
| ariaOrientation | Sets the orientation of the scrollbar as aria property `aria-orientation` | 'vertical'    | String          |

## Available slots

| Slot name  | Description                                                                      |
| ---------- | -------------------------------------------------------------------------------- |
| beforeRail | Content placed before the rail, like the arrows in the default chrome scrollbar. |
| afterRail  | Content placed after the rail, like the arrows in the default chrome scrollbar.  |
