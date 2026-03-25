import java.io.*;
import java.util.*;

/* =========================
Use Case 12:
Data Persistence & System Recovery
========================= */

class Reservation implements Serializable {
private static final long serialVersionUID = 1L;

    String bookingId;
    String guestName;
    String roomType;

    public Reservation(String bookingId, String guestName, String roomType) {
        this.bookingId = bookingId;
        this.guestName = guestName;
        this.roomType = roomType;
    }

    @Override
    public String toString() {
        return bookingId + " | " + guestName + " | " + roomType;
    }
}

class SystemState implements Serializable {
private static final long serialVersionUID = 1L;

    Map<String, Integer> inventory = new HashMap<>();
    List<Reservation> bookingHistory = new ArrayList<>();
}

class PersistenceService {

    private static final String FILE_NAME = "system_state.dat";

    // SAVE STATE (Serialization)
    public static void save(SystemState state) {
        try (ObjectOutputStream out =
                     new ObjectOutputStream(new FileOutputStream(FILE_NAME))) {

            out.writeObject(state);
            System.out.println("State saved successfully.");

        } catch (IOException e) {
            System.out.println("Error saving state: " + e.getMessage());
        }
    }

    // LOAD STATE (Deserialization)
    public static SystemState load() {
        File file = new File(FILE_NAME);

        if (!file.exists()) {
            System.out.println("No saved state found. Starting fresh.");
            return new SystemState();
        }

        try (ObjectInputStream in =
                     new ObjectInputStream(new FileInputStream(FILE_NAME))) {

            System.out.println("State restored successfully.");
            return (SystemState) in.readObject();

        } catch (Exception e) {
            System.out.println("Corrupted data. Starting with clean state.");
            return new SystemState();
        }
    }
}

public class Main {

    public static void main(String[] args) {

        // ---- System Startup (Recovery) ----
        SystemState state = PersistenceService.load();

        // Initialize inventory if empty
        if (state.inventory.isEmpty()) {
            state.inventory.put("DELUXE", 5);
            state.inventory.put("STANDARD", 3);
        }

        // ---- Simulate Booking ----
        Reservation r1 =
                new Reservation(UUID.randomUUID().toString(),
                        "Rahul", "DELUXE");

        state.bookingHistory.add(r1);
        state.inventory.put(
                r1.roomType,
                state.inventory.get(r1.roomType) - 1
        );

        System.out.println("\nCurrent Bookings:");
        for (Reservation r : state.bookingHistory) {
            System.out.println(r);
        }

        System.out.println("\nInventory:");
        System.out.println(state.inventory);

        // ---- System Shutdown (Persistence) ----
        PersistenceService.save(state);
    }
}
