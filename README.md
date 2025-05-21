Accessing Table View Cell IndexPath from Button Action
This snippet demonstrates how to retrieve the indexPath of a UITableViewCell when a button inside the cell is tapped, allowing you to perform actions (e.g., showing a rename alert) in the parent view controller. This is useful for scenarios where a button’s action needs to reference the cell’s position in a UITableView.
Context
In a UIKit-based iOS app, you have a custom table view cell (SelectedFilesTableViewCell) with a button that triggers an @IBAction in the parent view controller. The goal is to determine the cell’s indexPath to perform an action, such as renaming a file associated with the cell’s row.
Code
@IBAction func renameSelectedFile(_ sender: UIButton) {
    guard let cell = sender.superview?.superview?.superview as? SelectedFilesTableViewCell,
          let indexPath = selectedFilesTableView.indexPath(for: cell) else { return }
    
    showRenameAlert(for: indexPath.row, isSelectedFile: true)
}

How It Works

Button Action: The @IBAction is connected to a button inside a custom UITableViewCell (e.g., SelectedFilesTableViewCell) in Interface Builder or programmatically.
Accessing the Cell:
sender is the UIButton that was tapped.
sender.superview?.superview?.superview navigates up the view hierarchy to reach the SelectedFilesTableViewCell. The number of superview calls depends on the view hierarchy (e.g., button → contentView → cell).
The as? SelectedFilesTableViewCell cast ensures the view is the expected cell type.


Getting the IndexPath:
selectedFilesTableView.indexPath(for: cell) retrieves the indexPath of the cell within the UITableView (selectedFilesTableView is the table view outlet in the view controller).


Using the IndexPath:
The indexPath.row is used to identify the row (e.g., to access a specific file in an array).
The showRenameAlert(for:isSelectedFile:) method is called to display a rename alert, passing the row index.



Setup Requirements

Table View: Ensure the UITableView outlet (selectedFilesTableView) is connected in your view controller.
Custom Cell: Create a SelectedFilesTableViewCell class (subclass of UITableViewCell) with a button configured in Interface Builder or code.
Button Connection: Connect the button’s touch-up-inside event to the renameSelectedFile action in the view controller via Interface Builder or programmatically.
View Hierarchy: Verify the number of superview calls matches your cell’s structure. For example:
Button → contentView → UITableViewCell requires two superview calls.
Adjust based on your layout (e.g., three superview calls if the button is inside an additional container view).



Example View Controller
class ViewController: UIViewController {
    @IBOutlet weak var selectedFilesTableView: UITableView!
    
    override func viewDidLoad() {
        super.viewDidLoad()
        selectedFilesTableView.delegate = self
        selectedFilesTableView.dataSource = self
        selectedFilesTableView.register(SelectedFilesTableViewCell.self, forCellReuseIdentifier: "SelectedFilesCell")
    }
    
    @IBAction func renameSelectedFile(_ sender: UIButton) {
        guard let cell = sender.superview?.superview?.superview as? SelectedFilesTableViewCell,
              let indexPath = selectedFilesTableView.indexPath(for: cell) else { return }
        
        showRenameAlert(for: indexPath.row, isSelectedFile: true)
    }
    
    func showRenameAlert(for row: Int, isSelectedFile: Bool) {
        let alert = UIAlertController(title: "Rename File", message: "Enter new file name", preferredStyle: .alert)
        alert.addTextField { textField in
            textField.placeholder = "New file name"
        }
        alert.addAction(UIAlertAction(title: "OK", style: .default) { _ in
            if let newName = alert.textFields?.first?.text {
                print("Renaming file at row \(row) to \(newName)")
                // Update your data model and reload the table view
            }
        })
        alert.addAction(UIAlertAction(title: "Cancel", style: .cancel))
        present(alert, animated: true)
    }
}

// Example UITableViewDataSource
extension ViewController: UITableViewDataSource {
    func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        return 10 // Replace with your data count
    }
    
    func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
        let cell = tableView.dequeueReusableCell(withIdentifier: "SelectedFilesCell", for: indexPath) as! SelectedFilesTableViewCell
        // Configure cell
        return cell
    }
}

Alternative Approach
Instead of traversing the view hierarchy with superview, you can tag the button or pass the indexPath directly:

Using Tags:
Set the button’s tag to the indexPath.row in cellForRowAt:cell.renameButton.tag = indexPath.row
cell.renameButton.addTarget(self, action: #selector(renameSelectedFile(_:)), for: .touchUpInside)


In the action:@IBAction func renameSelectedFile(_ sender: UIButton) {
    showRenameAlert(for: sender.tag, isSelectedFile: true)
}




Using Closures:
Add a closure property to SelectedFilesTableViewCell:class SelectedFilesTableViewCell: UITableViewCell {
    var renameAction: ((Int) -> Void)?
    
    @IBAction func renameButtonTapped(_ sender: UIButton) {
        renameAction?(tag) // Set tag to indexPath.row in cellForRowAt
    }
}


Configure in cellForRowAt:cell.tag = indexPath.row
cell.renameAction = { [weak self] row in
    self?.showRenameAlert(for: row, isSelectedFile: true)
}





Notes

Safety: The guard statement ensures the cell and indexPath exist, preventing crashes.
Hierarchy Caution: The superview?.superview?.superview approach is fragile if the view hierarchy changes. Consider the closure or tag approach for robustness.
Usage: This snippet is ideal for actions like renaming files, deleting rows, or triggering alerts based on the cell’s position.

Example File
This snippet assumes a SelectedFilesTableViewCell with a button connected to renameSelectedFile. Save this in your swiftSnip repo as TableViewCellIndexPath.md for easy reference.
