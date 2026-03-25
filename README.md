import java.util.*;

class AddOnService {
private String serviceName;
private double cost;

    public AddOnService(String serviceName, double cost) {
        this.serviceName = serviceName;
        this.cost = cost;
    }

    public String getServiceName() {
        return serviceName;
    }

    public double getCost() {
        return cost;
    }
}

class AddOnServiceManager {

    private Map<String, List<AddOnService>> reservationServices;

    public AddOnServiceManager() {
        reservationServices = new HashMap<>();
    }

    public void addService(String reservationId, AddOnService service) {
        reservationServices
                .computeIfAbsent(reservationId, k -> new ArrayList<>())
                .add(service);
    }

    public double calculateTotalCost(String reservationId) {
        double total = 0;

        List<AddOnService> services =
                reservationServices.getOrDefault(reservationId, new ArrayList<>());

        for (AddOnService s : services) {
            total += s.getCost();
        }

        return total;
    }

    public void displayServices(String reservationId) {

        System.out.println("Add-On Services for Reservation " + reservationId);

        List<AddOnService> services =
                reservationServices.getOrDefault(reservationId, new ArrayList<>());

        for (AddOnService s : services) {
            System.out.println(s.getServiceName() + " - ₹" + s.getCost());
        }

        System.out.println("Total Add-On Cost: ₹" + calculateTotalCost(reservationId));
    }
}

public class HotelBookingApplication {

    public static void main(String[] args) {

        AddOnServiceManager manager = new AddOnServiceManager();

        String reservationId = "RES101";

        manager.addService(reservationId, new AddOnService("Breakfast", 500));
        manager.addService(reservationId, new AddOnService("Airport Pickup", 1200));
        manager.addService(reservationId, new AddOnService("Extra Bed", 800));

        manager.displayServices(reservationId);
    }
}
