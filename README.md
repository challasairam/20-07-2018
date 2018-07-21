# 20-07-2018
@RequestMapping("getRoomDetails")
	public ModelAndView getRoomDetails(
			@ModelAttribute("room") RoomDetails details) {
		ModelAndView view = new ModelAndView();
		try {
			ArrayList<Hotel> hotels = service.getHotelList();
			if (!hotels.isEmpty()) {

				boolean res = service.checkHotelId(details.getHotelId());
				if (res) {
					ArrayList<RoomDetails> list = service
							.getRoomDetails(details.getHotelId());
					System.out.println(list);
					if (!list.isEmpty()) {
						view = new ModelAndView("DisplayForUser", "room",
								new RoomDetails());
						view.addObject("roomDetails", list);
						view.addObject("hotelList", hotels);
					} else {
						view = new ModelAndView("DisplayForUser", "room",
								new RoomDetails());
						String msg = "No rooms are available in this hotel for Booking!";
						view.addObject("errorMessage", msg);
						view.addObject("hotelList", hotels);
					}
				} else {
					view = new ModelAndView("DisplayForUser", "room",
							new RoomDetails());
					String msg = "No hotel exists with such hotel id!";
					view.addObject("errorMessage", msg);
					view.addObject("hotelList", hotels);
				}
			} else {
				view = new ModelAndView("DisplayForUser", "room",
						new RoomDetails());
				String msg = "No hotels are available for Booking!";
				view.addObject("msg", msg);

			}
		} catch (HotelException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
		return view;

	}

	@RequestMapping("BookRoom")
	public ModelAndView bookARoom(@ModelAttribute("room") RoomDetails details) {

		BookingDetails bookingDetails = new BookingDetails();
		ModelAndView view = new ModelAndView("BookRoom", "book", bookingDetails);
		view.addObject("roomId", details.getRoomId());
		System.out.println(details.getRoomId());
		return view;
	}

	@RequestMapping("insertBooking")
	public ModelAndView insertBookingDetails(
			@ModelAttribute("book") BookingDetails bookingDetails,
			HttpServletRequest request) {
		HttpSession session = request.getSession(false);
		ModelAndView view = new ModelAndView();
		Object s = session.getAttribute("userid");
		// System.out.println(bookingDetails.getRoomId());
		bookingDetails.setUserId(Integer.parseInt(s.toString()));
		try {
			BookingDetails bookingDetails2 = service
					.insertBookingDetails(bookingDetails);
			view.addObject("id", bookingDetails2.getBookingId());
			view = new ModelAndView("DisplayForUser", "room", new RoomDetails());
		} catch (HotelException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
		return view;
	}

	@RequestMapping("bookingStatus")
	public ModelAndView viewBookingStatus(HttpServletRequest request) {
		HttpSession session = request.getSession(false);
		ModelAndView view = new ModelAndView();
		BookingDetails details = new BookingDetails();
		Object s = session.getAttribute("userid");
		details.setUserId(Integer.parseInt(s.toString()));
		try {
			ArrayList<Hotel> hotels = service.getHotelList();
			if (!hotels.isEmpty()) {
				boolean r = service.viewBookingStatus(details.getUserId());
				System.out.println(r);
				if (r) {
					view = new ModelAndView("DisplayForUser", "room",
							new RoomDetails());
					String statusmsg1 = "You have already booked!";
					view.addObject("statusmsg1", statusmsg1);
					view.addObject("hotelList", hotels);
				} else {
					view = new ModelAndView("DisplayForUser", "room",
							new RoomDetails());
					String statusmsg2 = "Not yet booked!";
					view.addObject("statusmsg2", statusmsg2);
					view.addObject("hotelList", hotels);
				}
			}
			else {
				view = new ModelAndView("DisplayForUser", "room",
						new RoomDetails());
				String msg = "No hotels are available for Booking!";
				view.addObject("msg", msg);
			}
		} catch (HotelException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
		return view;
	}
