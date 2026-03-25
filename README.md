import java.util.LinkedList;
import java.util.Queue;

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

    public void display() {
        System.out.println("Guest: " + guestName + " | Room Type: " + roomType);
    }
}

class BookingRequestQueue {

    private Queue<Reservation> requestQueue;

    public BookingRequestQueue() {
        requestQueue = new LinkedList<>();
    }

    public void addRequest(Reservation reservation) {
        requestQueue.offer(reservation);
        System.out.println("Booking request added for " + reservation.getGuestName());
    }

    public void displayQueue() {
        System.out.println("\n===== Booking Request Queue =====");

        for (Reservation r : requestQueue) {
            r.display();
        }
    }
}

public class HotelBookingApplication {

    public static void main(String[] args) {

        BookingRequestQueue queue = new BookingRequestQueue();

        Reservation r1 = new Reservation("Amit", "Single Room");
        Reservation r2 = new Reservation("Neha", "Suite Room");
        Reservation r3 = new Reservation("Rahul", "Double Room");

        queue.addRequest(r1);
        queue.addRequest(r2);
        queue.addRequest(r3);

        queue.displayQueue();
    }
}
