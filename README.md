import java.util.*;

class Reservation {
private String reservationId;
private String guestName;
private String roomType;
private String roomId;
private boolean active;

    public Reservation(String reservationId, String guestName,
                       String roomType, String roomId) {
        this.reservationId = reservationId;
        this.guestName = guestName;
        this.roomType = roomType;
        this.roomId = roomId;
        this.active = true;
    }

    public String getReservationId() {
        return reservationId;
    }

    public String getRoomType() {
        return roomType;
    }

    public String getRoomId() {
        return roomId;
    }

    public boolean isActive() {
        return active;
    }

    public void cancel() {
        active = false;
    }

    public void display() {
        System.out.println(
                reservationId + " | " +
                guestName + " | " +
                roomType + " | Room ID: " +
                roomId + " | Status: " +
                (active ? "CONFIRMED" : "CANCELLED"));
    }
}

class InventoryService {

    private Map<String, Integer> inventory = new HashMap<>();

    public InventoryService() {
        inventory.put("Single Room", 1);
        inventory.put("Double Room", 1);
        inventory.put("Suite Room", 1);
    }

    public void increment(String roomType) {
        inventory.put(roomType, inventory.get(roomType) + 1);
    }

    public void displayInventory() {
        System.out.println("\nInventory State:");
        for (String key : inventory.keySet()) {
            System.out.println(key + " : " + inventory.get(key));
        }
    }
}

class BookingHistory {

    private Map<String, Reservation> reservations = new HashMap<>();

    public void addReservation(Reservation r) {
        reservations.put(r.getReservationId(), r);
    }

    public Reservation getReservation(String id) {
        return reservations.get(id);
    }

    public void displayAll() {
        System.out.println("\nBooking History:");
        for (Reservation r : reservations.values()) {
            r.display();
        }
    }
}

class CancellationService {

    private InventoryService inventory;
    private BookingHistory history;
    private Stack<String> rollbackStack = new Stack<>();

    public CancellationService(InventoryService inventory,
                               BookingHistory history) {
        this.inventory = inventory;
        this.history = history;
    }

    public void cancelReservation(String reservationId) {

        Reservation reservation = history.getReservation(reservationId);

        if (reservation == null) {
            System.out.println("Cancellation Failed: Reservation not found");
            return;
        }

        if (!reservation.isActive()) {
            System.out.println("Cancellation Failed: Already cancelled");
            return;
        }

        rollbackStack.push(reservation.getRoomId());

        inventory.increment(reservation.getRoomType());

        reservation.cancel();

        System.out.println("Cancellation Successful for "
                + reservationId +
                " | Released Room ID: "
                + rollbackStack.pop());
    }
}

public class HotelBookingApplication {

    public static void main(String[] args) {

        InventoryService inventory = new InventoryService();
        BookingHistory history = new BookingHistory();

        Reservation r1 =
                new Reservation("RES101", "Amit", "Single Room", "SI1");

        Reservation r2 =
                new Reservation("RES102", "Neha", "Suite Room", "SU1");

        history.addReservation(r1);
        history.addReservation(r2);

        CancellationService cancellationService =
                new CancellationService(inventory, history);

        cancellationService.cancelReservation("RES101");
        cancellationService.cancelReservation("RES101");
        cancellationService.cancelReservation("RES999");

        history.displayAll();
        inventory.displayInventory();
    }
}
