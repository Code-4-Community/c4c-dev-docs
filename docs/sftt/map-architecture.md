# General Overview

SFTT uses ArcGIS to handle the block location, shape, and reservation status. Block data is stored in an ArcGIS database. The reservation pages on the SFTT web application incorperate the ArcGIS JavaScript API: https://developers.arcgis.com/javascript/latest/guide/. User data is stored in a separate SFTT database.

The component that represents the webpages with the map is the `MapContainerView`. Navigate to one of the map pages via the 'Reserve New Blocks' or 'Reservations Map' as a normal user or through the 'Active Blocks Map' as an admin. 'Reserve New Blocks' will display open blocks, 'Reservations Map' displays blocks the user has reserved, and 'Active Blocks Map' displays all reserved and completed blocks.

The blocks that a user has reserved are stored in the SFTT database. Upon navigating to the 'Reservations Map', a get request for the user's reserved blocks is made. Then a query is made with these block IDs for the rest of the ArcGIS block data and the corresponding blocks are displayed on the map.

The map itself is the `MapComponent`. This allows users to move around Boston and interact with the blocks from ArcGIS. The colored shapes represent blocks and the red lines represent private streets. The `MapComponent` also allows users to find their location and to toggle labels for the blocks.

Clicking on of the colored blocks displays a popup with the information about a specific block, its ID and Reservation Status which is either open, reserved, or complete. Depending on the reservation status, a user is able to interact with blocks of different statuses in different ways. Clicking on one of the buttons in the bottom left corner of the popup adds it to a list of active blocks.

These lists are available above the `MapComponent` in the `SelectedBlocks` component. A user can open the popver which displays the IDs of the currently selected blocks. Clicking the X on one of these blocks triggers a callback to the `MapContainerView` to remove that block from the selected list. Clicking the other button will trigger a Post request to the SFTT api to change the reservation status of each blocks in the active list. There are separate lists for reserving, releasing, and completing blocks.


# Map router

## Normal Routes 

### `/reserve/:editmode`

>editmode defaults to 'new' which routes to a MapContainerView with a map that displays blocks that are currently open (have not been reserved).

>editmde can also be set to 'edit' which routes to MapContainerView with a map that displays the blocks that the user currently has reserved.

>Can be passed activeBlockId as a prop which will highlight the block with the given ID.

## Admin Routes

### `/admin-map`

>routes to a MapContainerView which displays all of the blocks that are currently reserved or completed by all users. Only accessible to users with ADMIN level privileges.

# MapContainerView

The routes to the map pages lead to the MapContainerView which acts as a container for the map component.


## Props

### `activeBlockId`

```
(Not Required)
Type: String
```

>When entering the MapContainerView from one of the reservation list pages, the id of the selected block is highlighted

## Data

### `blocksToReserve, blocksToRelease, blocksToComplete`

```
Type: Array of Strings
```

>A pending list of the block IDs in JSON format that can be reserved, released, or completed upon clicking the buttons at the top of the map. These lists are passed to the `SelectedBlocks` component and displayed in a dropdown

### `modalMessage, blockListString`

```
Type: String
```

>The message displayed in the modal after attempting to reserve, release, or complete a set of blocks

### `showHeader`

```
Type: Boolean
```

>If true, the test header will be visable at the top of the page, if false, it will be collapsed

## Computed

### `isAdminMap`

```
Returns: Boolean
```

>Determines if this is an AdminMap depending on the route. Admin maps allow users to interact with all of the reserved and completed blocks, as opposed to the normal reservation maps which only allow users to interat with the blocks they reserved themselves.

### `header`

```
Returns: {
  headerVal: String,
  subTitle: String
}
```

>The header strings to appear at the top of the page. Changes depending on the route

### `reservedFilter`

```
Returns: Number
```

>Determines the reservation status that the map will use as a filter depending on :editmode. returns 0 for 'new' and 1 for 'edit'

## Methods

### `pushBlock`

```
Inputs:
block: string
selection: 'reserve' | 'release' | 'complete'

Returns: void
```

>Converts the given block ID to JSON and adds it to the pending reserve, release, or complete list depending on the given selection. If the given ID is already in one of the pending lists, displays an error message.

### `reserveBlocks`

```
Inputs: None

Returns: void
```

>Converts the `blocksToReserve` Array from data to a string. calls the reserveBlocks api function which makes a post request `/reserve` with the string of block ids as the body. 

>If successful, resets `blocksToReserve` to an empty Array, displays a confirmation message, and refreshes the map to display the updated block statuses.

>If unsuccessful, display a failure message.

### `releaseBlocks, completeBlocks`

>Identical behavior to reserveBlocks except the JSON lists are blocksToRelease, `blocksToComplete` and the api routes called are `/release`, `/complete`


### `setReserveBlocks, setReleaseBlocks, setCompleteBlocks`

```
Inputs:
blocks: Array of strings

Returns: void
```

>Sets the corresponsing `blocksToReserve, blocksToRelease, blocksToComplete` list equal to the given blocks

# MapComponent

ArcGIS types are lazy loaded in `loadMap` method.

## Props


### `reservedFilter`

```
Type: Number
Required: false
```

>The Reservation status of blocks to be displayed on the map. 0 for open, 1 for reserved.

### `pushBlock`

```
Type: Function

Inputs:
block: string
selection: 'reserve' | 'release' | 'complete'

Returns: void


Required: false
```

>Callback from MapContainerView which adds the given block to one of the active block lists when an event from selecting one of the `blockActions` is triggered.

### `activeBlockId`

```
Type: string

Required: false
```

>The popup of the block with the `activeBlockId` will be open upon loading the map.

### `isAdminMap`

```
Type: Boolean

Required: false
```

>If true, all completed and reserved blocks will be displayed

## Data

### `labelsVisible`

```
Type: Boolean
```

>If true, the IDs of the blocks will be displayed on the map


## Created (Constants)


### `addToReserve, addToReserve, addToComplete`

```
Type: ActionButton (ArcGIS type)
```

>Each object represents one of the buttons on the `blockPopupTemplate`. Clicking on one of these buttons triggers an event which calls `pushBlock` with the appropriate selection.

For more information on actions: https://developers.arcgis.com/javascript/latest/api-reference/esri-support-actions-ActionButton.html

### `blockRenderer`

```
Type: Renderer (ArcGIS type)
```

>Tells the `blockLayer` how to display each of the blocks. Sets a different color for the different possible reservation statuses

For more information on Renderers: https://developers.arcgis.com/javascript/latest/api-reference/esri-renderers-Renderer.html

### `privateStreetRenderer`

```
Type: Renderer (ArcGIS type)
```

>Tells the `privateStreetLayer` how to display each of the private streets. Currently displays each street as a solid red line

### `blockLabel`

```
Type: LabelClass (ArcGIS type)
```

>How the optional ID labels on the map will be displayed.

For more information on LabelClasses: https://developers.arcgis.com/javascript/latest/api-reference/esri-layers-support-LabelClass.html

## Computed


### `mapState`

>Sets the `reservedBlocks` state to the user's reserved blocks from the VueX store.

### `blockFilter`

```
Returns: String
```

>The 'where' clause of a query that determines which blocks will be displayed on the map depending on their reservation status. If somehow the mapview is accessed through none of the proper routes, the filter will ensure no blocks are displayed.

### `blockPopupTemplate`

```
Returns: PopupTemplate (ArcGIS type)
```

>Contains the format of the popups that appear when clicking a block. Includes the ID, the resercation status, and a list of `blockAction`. Content displayed is determined by the contents of the selected feature (block). 

For more information on PopupTemplates: https://developers.arcgis.com/javascript/latest/api-reference/esri-PopupTemplate.html


### `blockActions`

```
Returns: Array of ActionButton (ArcGIS type)
```

>Determines which buttons will appear on the `blockPopupTemplate` depending on the `reservedFilter`.

### `blockLayerObject`

```
Returns: FeatureLayer (ArcGIS type)
```

>Contains the block data from ArcGIS, the rules for displaying blocks, and the interactions users can have with the blocks. 

>The `blockLayerObject` queries the ArcGIS database linked in the `url` field using the `blockFilter` as the WHERE clause. Each block is a feature.

For more information on FeatureLayers: https://developers.arcgis.com/javascript/latest/api-reference/esri-layers-FeatureLayer.html

### `privateStreetLayerObject`

```
Returns: FeatureLayer (ArcGIS type)
```

>Contains the private streets and the rules for displaying these streets on the map. Unlike with blocks there are no interactions available.


## Methods


### `showLabels`

```
Returns: void
```

> Changes `labelsVisible` from positive to negative and vice versa. Turns block labels on and off.

### `reservationToString`

```
Input: Feature (ArcGIS type)

Returns: string
```

>Converts the integer reservation status of a block as stored in ArcGIS to the correct string to be displayed in the popup. 0 -> Open, 1 -> Reserved, 2 -> Complete.

### `loadMap`

```
Returns: void
```

>Uses lazy loading to load the necessarily JS modules from ArcGIS: ArcGISMap, MapView, FeatureLayer, Locate.

>Initializes a blank map and a MapView. Mapview is the main container of all other map objects defined above. Adds the `blockLayerObject` and `privateStreetLayerObject` to the view. This allows the program to query for blocks from the ArcGIS database. 

>The locate object allows users to have the map center over their current real world location.

>There is an optional query after the map has loaded which finds the block associated with `activeBlockId` in the `blockLayer` and displays that block's popup.

For more information on MapViews: https://developers.arcgis.com/javascript/latest/api-reference/esri-views-MapView.html

## Mounted

Dispatches to VueX store with `getReservedBlocks` to get blocks from the api.

Calls `loadMap`.

## Watch

Calls `loadMap` when `reservedBlocks` changes, meaning once `reserveBlocks`, `releaseBlocks` or `completeBlocks` is called on the mapView. Map will now be displayed with the updated block reservation statuses.

Calls `loadMap` when `reservedFilter` changes. Because SFFT has a single page application, the page does not reset when changing to a differnt route that displays a map. Because of this the map needs to be rerendered to updated the block filter.

Calls `loadMap` when `labelsVisible` changes to account for updated labels.

# `SelectedBlocks`

A reusable component that takes a given list of block IDs. Users can view the list in a popover menu and have the option to remove the each individual ID from the list. There is also a button that allows users to trigger a call back. In this case the callback will activate one of the post requests from the `MapContainerComponent` with the list of block IDs.

## Props

### `onClick`

```
Type: Function

Inputs: None

Returns: Void
```

>A callback which is activated when clicking the button in the pending block popover. For the map this callback is `reserveBlocks`, `releaseBlocks`, or `completeBlocks`.

### `blocks`

```
Type: Array of string
```

>List of block IDs that will appear in the popover.

### `title`

```
Type: string
```

>The string to appear on the popover header and the popover button.

### `setBlocks`

```
Type: Function

Inputs: Array of string

Returns: void
```

>Callback which is called In the `removeBlock` method. Sets the array passed in `blocks` prop to the given array.

## Data

### `popoverToggle`

```
Type: Boolean
```

>Indicates if user has selected to open the popover.

## Computed

### `displayPopover`

```
Returns: Boolean
```

>Checks if user has selected to open the popover and that there is at least one block in `blocks`.

## Methods

### `removeBlock`

```
Input: string

Returns: void
```

>Removes the given block from `blocks` and then calls `setBlocks` with that array as an input.




























































