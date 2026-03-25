import java.util.*;

class Reservation {
private String guestName;
private String roomType;

    public Reservation(String guestName, String roomType) {
        this.guestName = guestName;
        this.roomType = roomType;
    }

    public String getGuestName() {
        return guestName;
    }

    public String getRoomType() {
        return roomType;
    }
}

class BookingRequestQueue {
private Queue<Reservation> queue = new LinkedList<>();

    public void addRequest(Reservation r) {
        queue.offer(r);
    }

    public Reservation getNextRequest() {
        return queue.poll();
    }

    public boolean isEmpty() {
        return queue.isEmpty();
    }
}

class InventoryService {
private HashMap<String, Integer> inventory = new HashMap<>();

    public InventoryService() {
        inventory.put("Single Room", 2);
        inventory.put("Double Room", 1);
        inventory.put("Suite Room", 1);
    }

    public int getAvailability(String type) {
        return inventory.getOrDefault(type, 0);
    }

    public void decrement(String type) {
        inventory.put(type, inventory.get(type) - 1);
    }

    public void displayInventory() {
        System.out.println("\nCurrent Inventory:");
        for (Map.Entry<String, Integer> e : inventory.entrySet()) {
            System.out.println(e.getKey() + " : " + e.getValue());
        }
    }
}

class BookingService {

    private BookingRequestQueue requestQueue;
    private InventoryService inventoryService;

    private Set<String> allocatedRoomIds = new HashSet<>();
    private HashMap<String, Set<String>> roomAllocations = new HashMap<>();

    private int idCounter = 1;

    public BookingService(BookingRequestQueue queue, InventoryService inventory) {
        this.requestQueue = queue;
        this.inventoryService = inventory;
    }

    private String generateRoomId(String roomType) {
        String id;
        do {
            id = roomType.substring(0, 2).toUpperCase() + idCounter++;
        } while (allocatedRoomIds.contains(id));

        allocatedRoomIds.add(id);
        return id;
    }

    public void processBookings() {

        while (!requestQueue.isEmpty()) {

            Reservation r = requestQueue.getNextRequest();
            String type = r.getRoomType();

            if (inventoryService.getAvailability(type) > 0) {

                String roomId = generateRoomId(type);

                roomAllocations
                        .computeIfAbsent(type, k -> new HashSet<>())
                        .add(roomId);

                inventoryService.decrement(type);

                System.out.println("Reservation Confirmed:");
                System.out.println("Guest: " + r.getGuestName());
                System.out.println("Room Type: " + type);
                System.out.println("Assigned Room ID: " + roomId);
                System.out.println();
            } else {
                System.out.println("Reservation Failed for "
                        + r.getGuestName()
                        + " (No availability for " + type + ")");
            }
        }
    }
}

public class HotelBookingApplication {

    public static void main(String[] args) {

        BookingRequestQueue queue = new BookingRequestQueue();
        InventoryService inventory = new InventoryService();

        queue.addRequest(new Reservation("Amit", "Single Room"));
        queue.addRequest(new Reservation("Neha", "Suite Room"));
        queue.addRequest(new Reservation("Rahul", "Single Room"));
        queue.addRequest(new Reservation("Priya", "Double Room"));

        BookingService bookingService = new BookingService(queue, inventory);

        bookingService.processBookings();

        inventory.displayInventory();
    }
}
