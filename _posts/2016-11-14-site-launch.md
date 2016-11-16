---
layout : post
title : "Bradley's Java Programming Blog Site Launches"
date : 2016-11-14
---

## Introduction
To help me understand new and existing Java Frameworks, I created this blog to document my journey.  In 2010, I went back to school to complete my Bachelors and Masters in Computer Science.  After I graduated with my Masters in Computer Science in the fall of 2015, I quickly realized that much of my experience was out of date - Servlets and JSP.  As I saught employment, most companies were looking for Spring/Hibernate or Full Stack Developers.  I had little experience with Javascript, and I had even less with Spring and Hibernate.

## Spring Framework
A few months ago I started tinkering with Spring Boot and MVC, and I instantly gravitated towards it.  It was a refreshingly, new experience to create Web Services / MVC applications than I was accustomed to using Servlets and JSP.  One of the first projects I tried to convert was my group Software Engineering project from my Bachelors program.

### Quintessential Program for Any Language
[Spring Framework](http://www.spring.io) has a cornucopia of information regarding to Spring, and I highly recommend this valuable resource to any aspiring Software Engineer / Developer interested in Spring.  Their [Guides](http://spring.io/guides) provide enough information for you to grasp the basics and propel you to learn more.

"Hello World."  Two words that spark imagination for any developer learning a new language.  These two words was a gateway for learning many languages throughout my career.  And, like every language I learnt in the past, I created an introductory Spring Boot and MVC applications for discovery and knowledge.  But, like many "Hello World" programs, you are eager to learn and experience more.  I quickly found myself seeking to expand my knowledge and discover more of what Spring had to offer.

### University Team Software Engineering Team Project (2012)
Our group consisted of seven students; and each student had a particular role as well as coding responsibilities relating to the project.  For our group assignment, after much deliberation, we decided to use Servlets and JSP to construct our site to mimic an online airline reservation system.  The mistake I made dismissing it too fast without researching it.  Most of the students in my group did not have experience with web development, so they deferred many decisions to me - I was the Project Manager as well as Lead Developer on the project.  I based my decision on my prior experiences with Spring, but I didn't realize how the ecosystem has changed through the years.  When we were designing our system, I felt that we were re-inventing the wheel many times.  While this provided us with valuable experience, this cost us time and didn't allow us to benefit from existing, more mature frameworks, who experienced many of the challenges that we encountered throughout our project.

#### Conversion
First, I had to create an Index Controller, and I had to configure Spring to render the JSP / JSTL pages.  In the application.properties file, I stipulated the location of the Views - in this case, JSP.

```java
@Controller
public class IndexController {

	@RequestMapping("/")
	protected ModelAndView index() {
		ModelAndView modelAndView =  new ModelAndView("index");
		modelAndView.addObject("availableFlights", new AvailableFlights());
		return modelAndView;
	}
}
```

```
### application.properties

spring.mvc.view.prefix: /WEB-INF/jsp/
spring.mvc.view.suffix: .jsp
```
In our project, we had many servlets, so I began with converting each servlet into a Controller.  During the process, I had to create some form beans.  Unfortunately, I did not have this POJO created in the original project.

```java
public class AvailableFlights {

		@NotNull
		@DateTimeFormat(pattern = "MM/dd/yyyy")
		private Date departureDate;
		
		@NotNull
		private String destination;
		
		@NotNull
		private String departure;

		public Date getDepartureDate() {
			return departureDate;
		}

		public void setDepartureDate(Date departureDate) {
			this.departureDate = departureDate;
		}

		public String getDestination() {
			return destination;
		}

		public void setDestination(String destination) {
			this.destination = destination;
		}

		public String getDeparture() {
			return departure;
		}

		public void setDeparture(String departure) {
			this.departure = departure;
		}

		@Override
		public String toString() {
			return "AvailableFlights [departureDate=" + departureDate + ", destination=" + destination + ", departure="
					+ departure + "]";
		}
}
```

Here is an example of one of the Servlets:
```java
	/**
	 * @see HttpServlet#doPost(HttpServletRequest request, HttpServletResponse response)
	 */
	protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		boolean hasError = false;
		Map<String, String> messages = new HashMap<String, String>();
		Map<String, Object> map = new HashMap<String, Object>();
		List<Flight> flights;
		
		String departureDate = request.getParameter("departure_date");
		String destination = request.getParameter("destination_location");
		String departure = request.getParameter("departure_location");
		
		System.out.println(departureDate);
		System.out.println(destination);
		System.out.println(departure);
		
		Date departDate = null;
		DateFormat df = new SimpleDateFormat("MM/dd/yyyy");
		if ( departureDate != null && !departureDate.trim().isEmpty() ) {
			if ( departureDate.equals("Date (required)")) {
				messages.put("MISSING_DATE", "Departure Date is required.");
				request.setAttribute("Error", messages);
				request.getRequestDispatcher("index.jsp").forward(request, response);
				return;
			}
			
			try {
				departDate = df.parse(departureDate);
			} catch (ParseException e) {
				df = new SimpleDateFormat("yyyy-MM-dd");
				try {
					departDate = df.parse(departureDate);
				} catch (ParseException e1) {
					e1.printStackTrace();
					messages.put("MISSING_DATE", "Departure Date is required.");
					request.setAttribute("Error", messages);
					request.getRequestDispatcher("index.jsp").forward(request, response);
					return;
				}
			}
		}
		
		City destinationCity = City.fromString(destination);
		City departureCity = City.fromString(departure);
		Location destinationLocation = Location.getLocationByCity(destinationCity);
		Location departureLocation = Location.getLocationByCity(departureCity);
		DateLocale dateLocaleDestination = new DateLocale(destinationLocation, new Date());
		DateLocale dateLocaleDeparture = new DateLocale(departureLocation, departDate);
		
		System.out.println(dateLocaleDestination.toString());
		System.out.println(dateLocaleDeparture.toString());
		
		map.put(FlightDatabaseService.DATE, departDate);
		map.put(FlightDatabaseService.DEPARTURE, dateLocaleDeparture.getLocation().getCity());
		map.put(FlightDatabaseService.DESTINATION, dateLocaleDestination.getLocation().getCity());
		ServiceProviderInterface service = new FlightServicesProvider();
		DatabaseService database = (FlightDatabaseService)service.getService(DatabaseService.class.getName());
		if ( database != null ) {
			flights = (List<Flight>) database.retrieve(map);
			request.getSession().setAttribute("flights", flights);
			if(request.getSession().getAttribute("admin") != null){
				request.getRequestDispatcher("adminAvailableFlights.jsp").forward(request, response);
			}else{
			request.getRequestDispatcher("availableFlights.jsp").forward(request, response);
			}
		} else {
			messages.put("NO_DATABASE", "An error occurred during processing, please try again.");
			request.setAttribute("Error", messages);
			request.getRequestDispatcher("index.jsp").forward(request, response);
		}
	}
```
And here is the conversion:

```java
	@RequestMapping("/flights")
	public String checkAvailableFlights(@Valid @ModelAttribute("availableFlights") AvailableFlights availableFlights,
		BindingResult bindingResult, Model model) {

		if (bindingResult.hasErrors()) {
			return "index";
		}

		List<Flight> flights = this.flightService.listFlights(availableFlights.getDeparture(),
				availableFlights.getDestination(), availableFlights.getDepartureDate());
		model.addAttribute("flights", flights);

		return "availableFlights";
	}
```

One of the features I enjoyed was validation.  I was able to let Spring handle validation for me - which reduced many lines of code.  When needed, I created Validators for readability, maintainability, and testing which produced lean controllers that were not bloated with source code.

```java
@Documented
@Constraint(validatedBy = BagLimitValidator.class)
@Target( { ElementType.TYPE, ElementType.ANNOTATION_TYPE})
@Retention(RetentionPolicy.RUNTIME)
public @interface BagLimit {
	String message() default "{BagLimit}";
	Class<?>[] groups() default {};
	Class<? extends Payload>[] payload() default {};
}

public class BagLimitValidator implements ConstraintValidator<BagLimit, BookingInfo> {
	
	@Override
	public void initialize(BagLimit bag) {
		// left intentionally blank
	}

	@Override
	public boolean isValid(BookingInfo object, ConstraintValidatorContext ctx) {
		if ( object == null ) return true;
		
		Integer numberOfPassengers = object.getTotalPassengers();
		/* A passenger bag limit is 2 */
		Integer maxNumberOfBags = new Integer( 2 * numberOfPassengers );
		return !((object.getBags() / maxNumberOfBags) <= numberOfPassengers);
	}
}
```
While converting the Servlets, I had to re-implement many of the services we had.  I opted to use [Spring Data JPA](http://projects.spring.io/spring-data-jpa/) module, and it was a very convenient and easy module to use.  I was able to reduce hundreds of lines of boilerplate code as well as improving the readability and testing of the services.

```java
@Repository
public interface FlightRepository extends CrudRepository<Flight, Long> {

	@Query(value = "SELECT * FROM flight WHERE departure = :departure AND destination = :destination AND CONVERT(departure_time,char) LIKE :departureTime%", nativeQuery = true)
	List<Flight> findByDepartureAndDestinationAndDepartureTimeStartingWith( 
			@Param("departure") String departure, 
			@Param("destination") String destination, 
			@Param("departureTime") String departureTime );
	
	Flight findByFlightNumber( String flightNumber );
	
}

@Service
public class FlightService {
	
	@Autowired
	private FlightRepository flightRepository;
	
	public void setFlightRepository( FlightRepository flightRepository ) {
		this.flightRepository = flightRepository;
	}

	@Transactional
	public List<Flight> listFlights( String departure, String destination, Date departureTime ) {
		DateFormat df = new SimpleDateFormat("yyyy-MM-dd");
		String result = df.format(departureTime);
		return this.flightRepository.findByDepartureAndDestinationAndDepartureTimeStartingWith(departure, destination, result);
	}
	
	@Transactional
	public Flight getFlight( String flightNumber ) {
		return this.flightRepository.findByFlightNumber(flightNumber);
	}
}
```
I will not bore you with all of the details of the View, but I did not have difficulty with the transition.  Here is a simplified example:

```
<form:form commandName="availableFlights" method="post" action="flights">
    <form:select path="departure">
	    <form:option  value="Chicago" label="Chicago"/>
    </form:select>

    <form:select path="destination">
     	<form:option  value="Chicago" label="Chicago"/>
    </form:select>

    <form:input path="departureDate"/>

	<form:errors path="departure" cssClass="error"/>
	<form:errors path="destination" cssClass="error"/>
	<form:errors path="departureDate" cssClass="error"/>
	
    <INPUT id="u13" type="submit" class="u13" value="Search Flights" tabindex="4" >
</form:form>
```
## Improvements
### Testing
#### Mocking

### Thymeleaf
I want to convert JSP to use Thymeleaf.  I already created a few simple applications using Thymeleaf and Bootstrap.  One of the issues we had with JSP was prototyping.  We had to use static HTML pages to provide prototypes to the faculity.  One of my group members used a tool, I think it was called Azure, to create mockups.

### Bootstrap
[Bootstrap](http://getbootstrap.com/) allows me to rapidly develop the front-end of web project.  I have basic knowledge of css and javascript, and with this framework, I am more productive and allows me to focus on different areas of the system.  I am using Bootstrap in my Heartstone project.  I am using [WebJars](http://www.webjars.org) versions of Bootstrap in my pom.xml file.

```
	<dependency>
		<groupId>org.webjars</groupId>
		<artifactId>bootstrap</artifactId>
		<version>3.3.7-1</version>
	</dependency>
```

## Conclusion
So, with this site, I am trying new frameworks and applications to spearhead my growth using [Jekyll](http://jekyllrb.com) and Markdown to gather my thoughts and showcase my journey to the world.
