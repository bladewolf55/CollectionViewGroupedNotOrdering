https://github.com/dotnet/maui/issues/13027

### Description

CollectionView inherits from ReorderableItemsView, which itself has these two properties:

* CanMixGroups
* CanReorderItems

The purpose of CanMixGroups is to allow reordering of items between groups.

https://learn.microsoft.com/en-us/dotnet/api/microsoft.maui.controls.reorderableitemsview?view=net-maui-7.0

However, the Microsoft.Maui class `Controls\src\Core\Handlers\Items\ReorderableItemsViewHandler.Windows.cs` has this code:

```csharp
void HandleDragItemsStarting(object sender, DragItemsStartingEventArgs e)
{
	// Built in reordering only supports ungrouped sources & observable collections.
	var supportsReorder = Element != null && !Element.IsGrouped && Element.ItemsSource is INotifyCollectionChanged;
	if (supportsReorder)
	{
		// The AllowDrop property needs to be enabled when we start the drag operation.
		// We can't simply enable it when we set CanReorderItems because the VisualElementTracker also updates this property.
		// That means the tracker can overwrite any set we do in UpdateCanReorderItems.
		// To avoid that possibility, let's force it to true when the user begins to drag an item.
		// Reset it back to what it was when finished.
		_trackerAllowDrop = ListViewBase.AllowDrop;
		ListViewBase.AllowDrop = true;
	}
	else
	{
		e.Cancel = true;
	}
}
```

The above clearly disallows (on Windows) reordering between groups, and I haven't found any CollectionView code that overrides this behavior.

What good is CanMixGroups if you . . . can't?

### Steps to Reproduce

1. Clone maui-samples repository
2. Open 6.0/UserInterface/Views/CollectionViewDemos/
3. Edit VerticalListGroupingPage.xaml
    ```xml
        <CollectionView ItemsSource="{Binding Animals}"
                        IsGrouped="true"
                        CanReorderItems="True"
                        CanMixGroups="True">
    ```
4. Run Windows app, select Grouping Vertical list with DataTemplates

Expected: Reordering between groups
Actual: No reordering at all




### Link to public reproduction project repository

https://github.com/bladewolf55/CollectionViewGroupedNotOrdering.git

### Version with bug

7.0 (current)

### Last version that worked well

Unknown/Other

### Affected platforms

I was *not* able test on other platforms

### Affected platform versions

Windows 11, Android 34

### Did you find any workaround?

_No response_

### Relevant log output

_No response_