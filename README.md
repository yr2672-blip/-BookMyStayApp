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

class InventoryService {

    private Map<String, Integer> inventory = new HashMap<>();

    public InventoryService() {
        inventory.put("Single Room", 2);
        inventory.put("Double Room", 1);
    }

    public synchronized boolean allocateRoom(String roomType) {

        int available = inventory.getOrDefault(roomType, 0);

        if (available <= 0) {
            return false;
        }

        inventory.put(roomType, available - 1);

        return true;
    }

    public synchronized void displayInventory() {
        System.out.println("\nFinal Inventory:");
        for (String key : inventory.keySet()) {
            System.out.println(key + " : " + inventory.get(key));
        }
    }
}

class BookingQueue {

    private Queue<Reservation> queue = new LinkedList<>();

    public synchronized void addRequest(Reservation r) {
        queue.offer(r);
    }

    public synchronized Reservation getRequest() {
        return queue.poll();
    }
}

class ConcurrentBookingProcessor extends Thread {

    private BookingQueue bookingQueue;
    private InventoryService inventory;

    public ConcurrentBookingProcessor(String name,
                                      BookingQueue bookingQueue,
                                      InventoryService inventory) {
        super(name);
        this.bookingQueue = bookingQueue;
        this.inventory = inventory;
    }

    public void run() {

        while (true) {

            Reservation reservation = bookingQueue.getRequest();

            if (reservation == null)
                break;

            boolean success =
                    inventory.allocateRoom(reservation.getRoomType());

            if (success) {
                System.out.println(getName() +
                        " confirmed booking for "
                        + reservation.getGuestName()
                        + " (" + reservation.getRoomType() + ")");
            } else {
                System.out.println(getName() +
                        " failed booking for "
                        + reservation.getGuestName()
                        + " - No availability");
            }

            try {
                Thread.sleep(100);
            } catch (InterruptedException e) {
            }
        }
    }
}

public class HotelBookingApplication {

    public static void main(String[] args) {

        InventoryService inventory = new InventoryService();
        BookingQueue queue = new BookingQueue();

        queue.addRequest(new Reservation("Amit", "Single Room"));
        queue.addRequest(new Reservation("Neha", "Single Room"));
        queue.addRequest(new Reservation("Rahul", "Single Room"));
        queue.addRequest(new Reservation("Priya", "Double Room"));
        queue.addRequest(new Reservation("Karan", "Double Room"));

        Thread t1 =
                new ConcurrentBookingProcessor("Processor-1", queue, inventory);

        Thread t2 =
                new ConcurrentBookingProcessor("Processor-2", queue, inventory);

        Thread t3 =
                new ConcurrentBookingProcessor("Processor-3", queue, inventory);

        t1.start();
        t2.start();
        t3.start();

        try {
            t1.join();
            t2.join();
            t3.join();
        } catch (InterruptedException e) {
        }

        inventory.displayInventory();
    }
}
