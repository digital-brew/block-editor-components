# InnerBlockSlider

This component creates the admin UI for a horizontal slider using inner blocks for each slide within it.

This component is useful when need to implement a carousel/slider on a page, and want control of the front end implementation, but want to avoid having to build a custom admin interface for it.

Note 1: This block is currently only compatible with version 2 of the block schema.
Note 2: because this component uses InnerBlocks, you cannot use InnerBlocks for anything else in your parent block.

## Usage

* Import the inner block slider component, and use it within the edit component of your block.
* Import the stylesheet and include it within your editor styles.

Full example:

Carousel block.json

```json
{
	"$schema": "https://schemas.wp.org/trunk/block.json",
	"apiVersion": 2,
	"name": "humanmade/carousel",
	"title": "Image Carousel",
	"icon": "images-alt2",
	"description": "Display images in a carousel.",
	"version": "1.0.0",
	"attributes": {
		// ...any extra required attributes.
		// Note as slide data is stored as inner blocks, you do not need to register attributes for this.
	}
}
```

Carousel block JS.

```js
import { InnerBlockSlider } from '@humanmade/block-editor-components';
import React from 'react';

import { useBlockProps, useInnerBlocksProps, InspectorControls } from '@wordpress/block-editor';
import { registerBlockType } from '@wordpress/blocks';

import metadata from './block.json';

function Edit( { clientId } ) {
	return (
		<div { ...useBlockProps() }>
			<InspectorControls>
				{ /* if necessary, use inspector controls to add UI for any required functionality. */ }
			</InspectorControls>
			{ /* Note: because this component uses InnerBlocks, you cannot use InnerBlocks for anything else in your parent block.  */ }
			<InnerBlockSlider
				allowedBlock="core/image"
				parentBlockId={ clientId }
				slideLimit={ 10 }
			/>
		</div>
	);
}

function Save( props ) {
	return (
		<div { ...useBlockProps.save() }>
			<div { ...useInnerBlocksProps.save() } />
		</div>
	);
}

registerBlockType(
	metadata,
	{
		edit: Edit,
		save: Save,
	}
);
```

Note the examples here use block API version 2, and `useBlockProps`/`useInnerBlockProps` which gives us more control over the markup. This block is currently only compatible with version 2 of the block schema.

The above example code creates a slider using the core image block as each slide. However if you need more control over the markup or options for each slide, you can register a new `carousel-slide` block for this, and specify in the block.json that it should only be used as a child of carousel.

```json
{
	"$schema": "https://schemas.wp.org/trunk/block.json",
	"apiVersion": 2,
	"name": "humanmade/carousel-slide",
	"title": "Carousel",
	"icon": "images-alt2",
	"description": "Carousel slide",
	"version": "1.0.0",
	"parent": [ "humanmade/carousel" ],
	"attributes": {
		// ...any required attributes.
	}
}
```

## Props

| Name             | Type           | Default | Description                                   |
| ---------------- | -------------- | ------- | --------------------------------------------- |
| `allowedBlock`   | `string`       | `''`    | Block types to be allowed inside the slider   |
| `slideLimit`     | `integer`      | `10`    | Maximum number of slides.                     |
| `parentBlockId`  | `string`       | `''`    | Client ID of parent block. This is required.  |
| `showNavigation` | `bool`         | `true`  | Whether to show slide navigation/pagination.  |
| `perPage`        | `integer`      | `1`     | Show multiple slides per page.                |

## Examples

### Managing the state externally.

If you need to sync the slider component state it with some other external functionality, you may need to manage state in your code. In order to to this, you need to use the `InnerBlockSlider.Controlled` component rather than the standard container component exposed by the library.

```js
import InnerBlockSlider from '@humanmade/block-editor-components';

const [ currentItemIndex, setCurrentItemIndex ] = useState( 0 );

<InnerBlockSlider.Controlled
	// ... other props
	parentBlockId={ clientId }
	currentItemIndex={ currentItemIndex }
	setCurrentItemIndex={ setCurrentItemIndex }
/>
```

## Credit

This component is adapted from the [InnerBlockSlider component from 10up's Block Components]( https://github.com/10up/block-components/tree/develop/components/inner-block-slider).
