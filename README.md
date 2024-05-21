# Custom Scrollbar

Creates a custom scrollbar intended for use in specific cases where it is needed, eg tables where you dont want the default scrollbar.

## Basic usage

``` html
<CustomScrollbar aria-controls="__nuxt" class="absolute h-full top-0 right-0" />
```

## Props overview

#### CustomScrollbar's props overview

| Prop | Description | Default value | Data type |
| ---- | ----------- | ------------- | --------- |
| persistent | Enables the scrollbar always, even though the target is not scrollable | false | Boolean |
| ariaControls | Target element for scrolling. `"__nuxt"` is used for the whole page instead of individual elements. | null | String |
| ariaValueNow | Sets the first column of the table as the table header. | 0 | [String,Number] |
| ariaValuemin | Sets the minimum value of the scrollbar as aria property `aria-valuemin`. | 0 | [String,Number] |
| ariaValuemax | Sets the maximum value of the scrollbar as aria property `aria-valuemin`. | 100 | [String,Number] |
| ariaOrientation | Sets the orientation of the scrollbar as aria property `aria-orientation`. | 'vertical' | String |
| handleAriaLabel | Sets the `aria-label` for the handle. | 'Scrollbar handle' | String |
| handleStyle | For additional inline styling of the handle of the scrollbar. | null | [String,Object,Array] |
| handleClass | For additional classes for the handle of the scrollbar. | null | [String,Object,Array] |

## Available slots

| Slot name | Description |
| --------- | ----------- |
| beforeRail | Content placed before the rail, alike the arrows in the default chrome scrollbar. |
| afterRail | Content placed after the rail, alike the arrows in the default chrome scrollbar. |

### Slot methods

| Method | Description |
| ------ | ----------- |
| scrollBy | Adjusts the scrollbar's position by a specified number of pixels when given a parameter. Negative values scroll in the opposite direction |
| scrollToStart | Scrolls the scrollbar's rail to the beginning of the scrollbar. |
| scrollToEnd | Scrolls the scrollbar's rail to the end of the scrollbar. |