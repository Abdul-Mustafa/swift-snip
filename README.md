# Accessing Table View Cell IndexPath from Button Action

This snippet demonstrates how to retrieve the `indexPath` of a `UITableViewCell` when a button inside the cell is tapped, allowing you to perform actions (e.g., showing a rename alert) in the parent view controller. This is useful for scenarios where a button’s action needs to reference the cell’s position in a `UITableView`.

## Context
In a UIKit-based iOS app, you have a custom table view cell (`SelectedFilesTableViewCell`) with a button that triggers an `@IBAction` in the parent view controller. The goal is to determine the cell’s `indexPath` to perform an action, such as renaming a file associated with the cell’s row.

## Code
```swift
@IBAction func renameSelectedFile(_ sender: UIButton) {
    guard let cell = sender.superview?.superview?.superview as? SelectedFilesTableViewCell,
          let indexPath = selectedFilesTableView.indexPath(for: cell) else { return }
    
    showRenameAlert(for: indexPath.row, isSelectedFile: true)
}
