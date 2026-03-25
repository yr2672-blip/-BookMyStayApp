import java.util.ArrayList;
import java.util.List;

class Reservation {
private String reservationId;
private String guestName;
private String roomType;

    public Reservation(String reservationId, String guestName, String roomType) {
        this.reservationId = reservationId;
        this.guestName = guestName;
        this.roomType = roomType;
    }

    public String getReservationId() {
        return reservationId;
    }

    public String getGuestName() {
        return guestName;
    }

    public String getRoomType() {
        return roomType;
    }

    public void display() {
        System.out.println(
                "Reservation ID: " + reservationId +
                " | Guest: " + guestName +
                " | Room Type: " + roomType);
    }
}

class BookingHistory {

    private List<Reservation> history;

    public BookingHistory() {
        history = new ArrayList<>();
    }

    public void addReservation(Reservation reservation) {
        history.add(reservation);
    }

    public List<Reservation> getAllReservations() {
        return history;
    }
}

class BookingReportService {

    private BookingHistory bookingHistory;

    public BookingReportService(BookingHistory bookingHistory) {
        this.bookingHistory = bookingHistory;
    }

    public void displayAllBookings() {
        System.out.println("===== Booking History =====");

        for (Reservation r : bookingHistory.getAllReservations()) {
            r.display();
        }
    }

    public void generateSummaryReport() {

        int totalBookings = bookingHistory.getAllReservations().size();

        System.out.println("\n===== Booking Summary Report =====");
        System.out.println("Total Confirmed Bookings: " + totalBookings);
    }
}

public class HotelBookingApplication {

    public static void main(String[] args) {

        BookingHistory history = new BookingHistory();

        history.addReservation(new Reservation("RES101", "Amit", "Single Room"));
        history.addReservation(new Reservation("RES102", "Neha", "Suite Room"));
        history.addReservation(new Reservation("RES103", "Rahul", "Double Room"));

        BookingReportService reportService = new BookingReportService(history);

        reportService.displayAllBookings();
        reportService.generateSummaryReport();
    }
}
