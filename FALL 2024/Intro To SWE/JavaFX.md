  

1. Consists of three layers:
    
    Stage: base window
    
    Scene
    
    Scene nodes: layout managers
    
      
    
2. stage.show() at the end to display the window.
3. Stage needs a scene to display

  

1. Scene is created like:
    
    `Scene scene = new Scene(root-node, Color.PINK);` also give the scene color.
    
      
    
2. `root-node` can be of different types:
    
    We are using Group type here:
    
    `Group root = new Group();`
    
    and then finally,
    
    ```Java
    stage.setScene(scene);
    stage.show();
    Image icon = new Image("path");
    Stage.getIcons().add(icon);
    ```
    

  

1. stage.setTitle() to set title
2. Other usefule setup methods:
    
    stage.setWidth();
    
    stage.setHeight();
    
    stage.setResizable(false);
    
    stage.setX(50); //where the render
    
    stage.setY(50);
    
      
    
    stage.setFullScreen(true);
    
    stage.setFullScreenExitHint(”PRESS Q”);
    
    stage.setFullScreenExitKeyCombination(KeyCombination.valueOf(”q”));
    

  

1. The following code will help review concepts always:
    
    ```Python
    /**
     * AUTHOR: VINNIE KUMAR
     * ASUID: 1226867709
     * ASSIGNMENT: HOMEWORK 1
     */
    
    
    import javafx.application.Application;
    import javafx.geometry.Insets;
    import javafx.geometry.Pos;
    import javafx.scene.Scene;
    import javafx.scene.control.*;
    import javafx.scene.layout.HBox;
    import javafx.scene.layout.VBox;
    import javafx.scene.paint.Color;
    import javafx.scene.text.TextAlignment;
    import javafx.stage.Stage;
    import javafx.scene.text.Text;
    import javafx.scene.text.Font;
    import javafx.scene.shape.Line;
    
    public class hw1 extends Application {
    
        // Arrays to store prices for food and drinks
        private double foodPrices[] = {7.99, 9.99, 2.50, 4.49}; // Prices for: Egg Sandwich, Chicken Sandwich, Bagel, Potato Salad
        private double drinkPrices[] = {1.25, 0.99, 1.99, 2.25};  // Prices for: Black Tea, Green Tea, Coffee, Orange Juice
        private double tax = 0.07; // Sales tax rate (7%)
    
        // Text area to display the bill
        private TextArea billArea = new TextArea();
        private double total = 0.0; // Variable to keep track of the total cost
    
        @Override
        public void start(Stage stage) {
    
            // Header section with background color and title text
            HBox head = new HBox(20);
            head.setStyle("-fx-background-color: yellowgreen");
            Text header = new Text("JOE's DELI");
            header.setFont(Font.font("Verdana", 50));
            header.setFill(Color.AZURE);
            header.setTextAlignment(TextAlignment.CENTER);
            head.setAlignment(Pos.CENTER);
            head.getChildren().add(header);
    
            // Create a line under the header for visual separation
            Line line = new Line(0, 0, 900, 0);
            line.setStrokeWidth(5);
            line.setStroke(Color.DARKGREEN);
    
            // Food section with checkboxes for food items
            VBox foodBox = new VBox(10);
            foodBox.setPadding(new Insets(10));
            foodBox.setAlignment(Pos.CENTER_LEFT); // Align items to the left within the box
            Text foodHeader = new Text("Food");
            CheckBox food1 = new CheckBox("Egg Sandwich");
            CheckBox food2 = new CheckBox("Chicken Sandwich");
            CheckBox food3 = new CheckBox("Bagel");
            CheckBox food4 = new CheckBox("Potato Salad");
            foodBox.getChildren().addAll(foodHeader, food1, food2, food3, food4);
    
            // Drink section with radio buttons for drink options
            VBox drinkBox = new VBox(10);
            drinkBox.setPadding(new Insets(10));
            drinkBox.setAlignment(Pos.CENTER_LEFT); // Align items to the left within the box
            Text drinkHeader = new Text("Drinks");
            ToggleGroup group = new ToggleGroup();
            RadioButton drink1 = new RadioButton("Black Tea");
            drink1.setToggleGroup(group);
            RadioButton drink2 = new RadioButton("Green Tea");
            drink2.setToggleGroup(group);
            RadioButton drink3 = new RadioButton("Coffee");
            drink3.setToggleGroup(group);
            RadioButton drink4 = new RadioButton("Orange Juice");
            drink4.setToggleGroup(group);
            drinkBox.getChildren().addAll(drinkHeader, drink1, drink2, drink3, drink4);
    
            // Bill section to display the order summary
            VBox billBox = new VBox(10);
            billBox.setPadding(new Insets(10));
            billBox.setAlignment(Pos.CENTER); // Center content within the box
            Text billHeader = new Text("Bill");
            billArea.setEditable(false); // Make the bill area non-editable
            billBox.getChildren().addAll(billHeader, billArea);
    
            // Buttons for ordering, canceling, and confirming
            HBox buttonBox = new HBox(10);
            buttonBox.setStyle("-fx-background-color: yellowgreen");
            buttonBox.setPadding(new Insets(10));
            buttonBox.setAlignment(Pos.CENTER); // Center the buttons
            Button orderButton = new Button("Order");
            Button cancelButton = new Button("Cancel");
            Button confirmButton = new Button("Confirm");
            buttonBox.getChildren().addAll(orderButton, cancelButton, confirmButton);
    
            // Add event handlers for button actions
            orderButton.setOnAction(e -> calculateBill(food1, food2, food3, food4, drink1, drink2, drink3, drink4));
            cancelButton.setOnAction(e -> cancelOrder(food1, food2, food3, food4, drink1, drink2, drink3, drink4));
            confirmButton.setOnAction(e -> confirmOrder(food1, food2, food3, food4, drink1, drink2, drink3, drink4));
    
            // Main layout of the application
            HBox mainLayout = new HBox(20); // Space between sections
            mainLayout.setStyle("-fx-background-color: yellowgreen");
            mainLayout.setPadding(new Insets(10));
            mainLayout.setAlignment(Pos.CENTER); // Center the content within the box
            mainLayout.getChildren().addAll(foodBox, drinkBox, billBox);
    
            // Root layout with header, line, main layout, and buttons
            VBox root = new VBox(10); // Space between elements
            root.setStyle("-fx-background-color: yellowgreen;");
            root.getChildren().addAll(head, line, mainLayout, buttonBox);
    
            // Set up the scene and stage
            Scene scene = new Scene(root, 900, 600);
            scene.setFill(Color.YELLOWGREEN);
            stage.setTitle("Joe's Deli");
            stage.setScene(scene);
            stage.setResizable(false);
            stage.show();
        }
    
        // Calculate the bill based on selected items and update the text area
        private void calculateBill(CheckBox food1, CheckBox food2, CheckBox food3, CheckBox food4, RadioButton drink1, RadioButton drink2, RadioButton drink3, RadioButton drink4) {
            String billText = "Items Ordered:\n";
    
            // Add selected food items to the bill
            if (food1.isSelected()) {
                billText += "Egg Sandwich ---- $7.99\n";
                total += foodPrices[0];
            }
            if (food2.isSelected()) {
                billText += "Chicken Sandwich ---- $9.99\n";
                total += foodPrices[1];
            }
            if (food3.isSelected()) {
                billText += "Bagel ---- $2.50\n";
                total += foodPrices[2];
            }
            if (food4.isSelected()) {
                billText += "Potato Salad ---- $4.49\n";
                total += foodPrices[3];
            }
    
            // Add selected drink item to the bill
            if (drink1.isSelected()) {
                billText += "Black Tea ---- $1.25\n";
                total += drinkPrices[0];
            } else if (drink2.isSelected()) {
                billText += "Green Tea ---- $0.99\n";
                total += drinkPrices[1];
            } else if (drink3.isSelected()) {
                billText += "Coffee ---- $1.99\n";
                total += drinkPrices[2];
            } else if (drink4.isSelected()) {
                billText += "Orange Juice ---- $2.25\n";
                total += drinkPrices[3];
            }
    
            // Display the bill text in the text area
            billArea.setText(billText);
        }
    
        // Clear all selections and the bill
        private void cancelOrder(CheckBox food1, CheckBox food2, CheckBox food3, CheckBox food4, RadioButton drink1, RadioButton drink2, RadioButton drink3, RadioButton drink4) {
            food1.setSelected(false);
            food2.setSelected(false);
            food3.setSelected(false);
            food4.setSelected(false);
            drink1.setSelected(false);
            drink2.setSelected(false);
            drink3.setSelected(false);
            drink4.setSelected(false);
            billArea.clear(); // Clear the bill area
        }
    
        // Confirm the order, calculate total, and display final bill
        private void confirmOrder(CheckBox food1, CheckBox food2, CheckBox food3, CheckBox food4, RadioButton drink1, RadioButton drink2, RadioButton drink3, RadioButton drink4) {
            // Deselect all items
            food1.setSelected(false);
            food2.setSelected(false);
            food3.setSelected(false);
            food4.setSelected(false);
            drink1.setSelected(false);
            drink2.setSelected(false);
            drink3.setSelected(false);
            drink4.setSelected(false);
    
            // Calculate tax and final total
            double taxAmount = total * tax;
            double finalTotal = total + taxAmount;
    
            // Update the bill with subtotal, tax, and total
            String finalText = billArea.getText();
            System.out.print(finalText); // Debug Print
            System.out.print(542); // Debug Print
            finalText += String.format("\nSubtotal: $%.2f\n", total);
            finalText += String.format("Tax (7%%): $%.2f\n", taxAmount);
            finalText += String.format("Total: $%.2f\n", finalTotal);
    
            // Display the final bill in the text area
            billArea.setText(finalText);
    
            // Reset total for the next order
            total = 0.0;
        }
    
        public static void main(String[] args) {
            launch(args);
        }
    }
    ```