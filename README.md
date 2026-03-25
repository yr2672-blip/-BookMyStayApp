abstract class Room {
String roomType;
int beds;
double price;

    Room(String roomType, int beds, double price) {
        this.roomType = roomType;
        this.beds = beds;
        this.price = price;
    }

    void displayDetails() {
        System.out.println("Room Type: " + roomType);
        System.out.println("Beds: " + beds);
        System.out.println("Price per Night: ₹" + price);
    }
}

class SingleRoom extends Room {
SingleRoom() {
super("Single Room", 1, 2000);
}
}

class DoubleRoom extends Room {
DoubleRoom() {
super("Double Room", 2, 3500);
}
}

class SuiteRoom extends Room {
SuiteRoom() {
super("Suite Room", 3, 6000);
}
}

public class HotelBookingApplication {

    public static void main(String[] args) {

        Room single = new SingleRoom();
        Room dbl = new DoubleRoom();
        Room suite = new SuiteRoom();

        int singleAvailability = 5;
        int doubleAvailability = 3;
        int suiteAvailability = 2;

        System.out.println("===== Room Availability =====");

        single.displayDetails();
        System.out.println("Available: " + singleAvailability);
        System.out.println();

        dbl.displayDetails();
        System.out.println("Available: " + doubleAvailability);
        System.out.println();

        suite.displayDetails();
        System.out.println("Available: " + suiteAvailability);
    }
}
