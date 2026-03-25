import java.util.HashMap;
import java.util.Map;

abstract class Room {
String type;
double price;

    Room(String type, double price) {
        this.type = type;
        this.price = price;
    }

    void displayDetails() {
        System.out.println("Room Type: " + type);
        System.out.println("Price per Night: ₹" + price);
    }
}

class SingleRoom extends Room {
SingleRoom() {
super("Single Room", 2000);
}
}

class DoubleRoom extends Room {
DoubleRoom() {
super("Double Room", 3500);
}
}

class SuiteRoom extends Room {
SuiteRoom() {
super("Suite Room", 6000);
}
}

class RoomInventory {

    private HashMap<String, Integer> inventory;

    public RoomInventory() {
        inventory = new HashMap<>();
        inventory.put("Single Room", 5);
        inventory.put("Double Room", 0);
        inventory.put("Suite Room", 2);
    }

    public int getAvailability(String roomType) {
        return inventory.getOrDefault(roomType, 0);
    }

    public Map<String, Integer> getAllAvailability() {
        return inventory;
    }
}

class SearchService {

    private RoomInventory inventory;
    private HashMap<String, Room> rooms;

    public SearchService(RoomInventory inventory) {
        this.inventory = inventory;

        rooms = new HashMap<>();
        rooms.put("Single Room", new SingleRoom());
        rooms.put("Double Room", new DoubleRoom());
        rooms.put("Suite Room", new SuiteRoom());
    }

    public void searchAvailableRooms() {

        System.out.println("===== Available Rooms =====");

        for (Map.Entry<String, Integer> entry : inventory.getAllAvailability().entrySet()) {

            String type = entry.getKey();
            int available = entry.getValue();

            if (available > 0) {
                Room room = rooms.get(type);
                room.displayDetails();
                System.out.println("Available: " + available);
                System.out.println();
            }
        }
    }
}

public class HotelBookingApplication {

    public static void main(String[] args) {

        RoomInventory inventory = new RoomInventory();
        SearchService searchService = new SearchService(inventory);

        searchService.searchAvailableRooms();
    }
}
