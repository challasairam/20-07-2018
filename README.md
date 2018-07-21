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
	-------------------
	<%=session.getAttribute("username")%>
		<h3>Booking</h3>
		<form:form action="getRoomDetails.obj" modelAttribute="room">
  Enter Hotel Id to retrieve Room Details
 <form:input path="hotelId" pattern="^[1-9][0-9]{3}$" title="Please enter 4-digit hotel id"/>
			<form:errors path="hotelId" />
			<input type="submit" name="search" value="Search">
		</form:form>
		<c:set var="r" value="${roomDetails }" />
		<c:choose>
			<c:when test="${r!=null }">
				<table border="2">
					<tr>
						<th>Hotel Id</th>
						<th>Room Id</th>
						<th>Room No</th>
						<th>Room Type</th>
						<th>Per Night rate</th>
						<th>Availability</th>

					</tr>
					<c:forEach items="${roomDetails}" var="e">
						<tr>
							<form:form action="BookRoom.obj" modelAttribute="room">
								<td><form:hidden path="hotelId" value="${e.hotelId}" /> <c:out
										value="${e.hotelId}"></c:out></td>
								<td><form:hidden path="roomId" value="${e.roomId}" /> <c:out
										value="${e.roomId}"></c:out></td>
								<td><form:hidden path="roomNo" value="${e.roomNo}" /> <c:out
										value="${e.roomNo}"></c:out></td>
								<td><form:hidden path="roomType" value="${e.roomType}" />
									<c:out value="${e.roomType}"></c:out></td>
								<td><form:hidden path="perNightRate"
										value="${e.perNightRate}" /> <c:out value="${e.perNightRate}"></c:out></td>
								<td><form:hidden path="availability"
										value="${e.availability}" /> <c:out value="${e.availability}"></c:out></td>
								<td><input type="submit" value="Book Now" name="button"></td>
							</form:form>
						</tr>

					</c:forEach>

				</table>
			</c:when>
			<c:otherwise>
				<c:out value="${errorMessage}" />
			</c:otherwise>
		</c:choose>
