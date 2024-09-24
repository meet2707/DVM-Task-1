class MeetHotel:
    def __init__(self, hotel_name):
        self.hotel_name = hotel_name
        self.rooms = []
        self.bookings = []

    def add_room(self, room):
        self.rooms.append(room)

    def get_free_rooms(self, start_day, end_day):
        free_rooms = []
        for room in self.rooms:
            if room.is_free(start_day, end_day):
                free_rooms.append(room)
        return free_rooms

    def make_booking(self, customer, room_num, start_day, end_day):
        room = next((r for r in self.rooms if r.room_num == room_num), None)
        
        if room is None:
            print(f"Room {room_num} not found.")
            return
        
        if not room.is_free(start_day, end_day):
            print(f"Room {room_num} is not free from day {start_day} to day {end_day}.")
            return
        
        total_cost = room.price * (end_day - start_day)
        booking_id = len(self.bookings) + 1
        booking = Booking(booking_id, customer, room, start_day, end_day, total_cost)
        
        room.add_booked_days(start_day, end_day)
        self.bookings.append(booking)
        customer.add_booking(booking)
        
        print(f"Room {room_num} booked for {customer.name} from day {start_day} to day {end_day}.")

class Room:
    def __init__(self, room_num, room_type, price):
        self.room_num = room_num
        self.room_type = room_type
        self.price = price
        self.booked_days = []

    def is_free(self, start_day, end_day):
        for booked_start, booked_end in self.booked_days:
            if not (end_day <= booked_start or start_day >= booked_end):
                return False
        return True

    def add_booked_days(self, start_day, end_day):
        self.booked_days.append((start_day, end_day))

class Booking:
    def __init__(self, booking_id, customer, room, start_day, end_day, total_cost):
        self.booking_id = booking_id
        self.customer = customer
        self.room = room
        self.start_day = start_day
        self.end_day = end_day
        self.total_cost = total_cost

class Customer:
    def __init__(self, name, email, phone):
        self.name = name
        self.email = email
        self.phone = phone
        self.bookings = []

    def add_booking(self, booking):
        self.bookings.append(booking)

hotel = MeetHotel("Easy Stay")

hotel.add_room(Room(1, "Single", 100))
hotel.add_room(Room(2, "Double", 150))
hotel.add_room(Room(3, "Suite", 200))

guest = Customer("Jane Doe", "jane@example.com", "555-4321")

start_day = 1
end_day = 5

free_rooms = hotel.get_free_rooms(start_day, end_day)
print(f"Rooms available from day {start_day} to day {end_day}: {[room.room_num for room in free_rooms]}")

hotel.make_booking(guest, 1, start_day, end_day)

for booking in guest.bookings:
    print(f"Booking ID: {booking.booking_id}, Room: {booking.room.room_num}, Cost: {booking.total_cost}")
