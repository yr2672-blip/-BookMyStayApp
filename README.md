import java.util.HashMap;

class InvalidBookingException extends Exception {
public InvalidBookingException(String message) {
super(message);
}
}

class InventoryService {

    private HashMap<String, Integer> inventory;

    public InventoryService() {
        inventory = new HashMap<>();
        inventory.put("Single Room", 2);
        inventory.put("Double Room", 1);
        inventory.put("Suite Room", 0);
    }

    public boolean isValidRoomType(String roomType) {
        return inventory.containsKey(roomType);
    }

    public int getAvailability(String roomType) {
        return inventory.getOrDefault(roomType, 0);
    }

    public void decrement(String roomType) throws InvalidBookingException {
        int available = getAvailability(roomType);

        if (available <= 0) {
            throw new InvalidBookingException("No rooms available for " + roomType);
        }

        inventory.put(roomType, available - 1);
    }

    public void displayInventory() {
        System.out.println("\nCurrent Inventory:");
        for (String key : inventory.keySet()) {
            System.out.println(key + " : " + inventory.get(key));
        }
    }
}

class InvalidBookingValidator {

    public static void validate(String guestName,
                                String roomType,
                                InventoryService inventory)
            throws InvalidBookingException {

        if (guestName == null || guestName.trim().isEmpty()) {
            throw new InvalidBookingException("Guest name cannot be empty");
        }

        if (!inventory.isValidRoomType(roomType)) {
            throw new InvalidBookingException("Invalid room type selected");
        }

        if (inventory.getAvailability(roomType) <= 0) {
            throw new InvalidBookingException("Requested room is not available");
        }
    }
}

class BookingService {

    private InventoryService inventory;

    public BookingService(InventoryService inventory) {
        this.inventory = inventory;
    }

    public void bookRoom(String guestName, String roomType) {

        try {
            InvalidBookingValidator.validate(guestName, roomType, inventory);

            inventory.decrement(roomType);

            System.out.println("Booking confirmed for " + guestName +
                    " (" + roomType + ")");

        } catch (InvalidBookingException e) {
            System.out.println("Booking Failed: " + e.getMessage());
        }
    }
}

public class HotelBookingApplication {

    public static void main(String[] args) {

        InventoryService inventory = new InventoryService();
        BookingService bookingService = new BookingService(inventory);

        bookingService.bookRoom("Amit", "Single Room");
        bookingService.bookRoom("", "Double Room");
        bookingService.bookRoom("Neha", "Luxury Room");
        bookingService.bookRoom("Rahul", "Suite Room");

        inventory.displayInventory();
    }
}
