# Structure for surveys powered by Survaider


## Design

### Overall
- Select the unit to give feedback for [ONLY SHOW THIS PAGE WHEN SURVEY IS FOR A PARENT BUSINESS].
- Rating on a scale of 5.
- Rating for each aspect.
- For each aspect, if rating is below threshold, ask details about that aspect.
- Any additional thoughts.

### Detailed
**Note:** The exact text for each question and it's options will be provided in API.  

1. Page 0: **Select unit** [Only show this page when the survey is for a parent business]  
   - Search bar.
   - List suggestions below as the user starts typing.
   - Each suggestion is the name of a unit.
   - List of all units to be included in API (Absent when survey is for a unit).
   - If parent or not will be provided in API.
   - Question text and placeholder text for the text input will be provided in API  
     (Absent when survey is for a unit).
   - Don't show this page when the survey is for a unit.

1. Page 1: **Rating**  
   - On a scale of **5**.
   - **4** or **5** is considered **positive**.
   - **3 or below** is considered **negative**.
   - Go to next page on user input (i.e., No manual "next" button) after some delay.

1. Page 2: **Aspects rating**  
   - List of all aspects (will be provided in API).
   - Rating for each aspect on a scale of 5.
   - If rating is below threshold for any aspect, ask detailed question for that aspect.

1. Page 3.x: **Options for each aspect** [One page for each aspect that got rating below threshold]  
For each aspect that was rated below threshold in previous page:  
   - Multi select options.
   - What went wrong / what to improve for that aspect.
   - The exact text for each question and it's options will be provided in the API.

1. Page 4: **Thoughts**  
   - Textbox
   - Additional info that the respondent might want to convey.

1. Page 5: **Respondent details**  
   - Name, email and phone.
   - None is compulsory.

1. Page 6: **Thank you**  
   - Name of business/unit will be provided in API.
   - If parent then name is the business name.
   - If unit, name is the unit name.  

* **Reset to first page after a delay of 3 seconds.**


## API

Endpoint: **`/survey/<survey_id>`**  

`survey_id` for sending **GET** can be taken from url path of the webpage. The
url will be like `/survey/<survey_id>`.  

`survey_id` for sending **POST** can be taken from:  
  - `units.survey_id` [Based on the unit selected by the user in Page 0],  
    if the survey is for a parent.
  - URL for the survey, if it is for a unit (Same as the one used to send **GET**).  

**NOTE:** So, **for POST request**, `survey_id` always represents a unit and not a parent business.  
However, it may represent either a parent or a unit **for GET request**.  

### Methods:

#### **GET**

##### Version 1 (For parent survey):
```json
{
    "is_parent": true,
    "units_question": "Which hotel did you visit?",
    "units_placeholder_text": "Enter hotel name here",
    "units": [
        {"name": "Unit 1", "survey_id": "aZ3mDpQw67A8L5KXyMQ"},
        {"name": "Unit 2", "survey_id": "eprJWMVJmzlgeZZkmzX"},
        {"name": "Unit 3", "survey_id": "goZJO7VJKxB5grrkKx9"}
    ],
    "business_name": "Sterling Hotels",
    "logo_link": "https://s3.ap-south-1.amazonaws.com/survaider-static-files/logos/sterling_medium.png",
    "rating_threshold": 4,
    "aspect_question_positive": "Thank you! Also, please let us know what can we improve.",
    "aspect_question_negative": "We are sorry to hear that! What went wrong?",
    "aspect_options": [
        {
            "aspect": "Cleanliness",
            "question_positive": "What can we improve in Cleanliness?",
            "question_negative": "What did you not like about Cleanliness?",
            "options": {
    			"1": "Bathroom and toiletries",
    			"2": "Bed, cover and upholstery",
    			"3": "Kitchen",
    			"4": "Lobby, the interiors in general"
    		}
        },
        {
            "aspect": "Value For Money",
            "question_positive": "...",
            "question_negative": "...",
            "options": {}
        },
        {
            "aspect": "Rooms",
            "question_positive": "...",
            "question_negative": "...",
            "options": {}
        },
        {
            "aspect": "Service",
            "question_positive": "...",
            "question_negative": "...",
            "options": {}
        },
        {
            "aspect": "Location",
            "question_positive": "...",
            "question_negative": "...",
            "options": {}
        }
	]
}
```

**Details:**  
* `business_name` is the name of the parent business.
* `units_question` and `units_placeholder_text` hold the value for text in page **0**.
* When rating for an aspect is positive (4 or 5):
  * `question_positive` should be the question text in pages **3.x**.
* When rating for an aspect is negative (3 or less):
  * `question_negative` should be the question text in pages **3.x**.
* Thank you page (page **6**) should show name of parent business / name of the unit
  based on whether it is a parent or a unit.


##### Version 2 (For unit survey):
```json
{
    "is_parent": false,
    "unit_name": "Sterling Goa",
    "logo_link": "https://s3.ap-south-1.amazonaws.com/survaider-static-files/logos/sterling_medium.png",
    "rating_threshold": 4,
    "aspect_question_positive": "Thank you! Also, please let us know what can we improve.",
    "aspect_question_negative": "We are sorry to hear that! What went wrong?",
    "aspect_options": [
        {
            "aspect": "Cleanliness",
            "question_positive": "What can we improve in Cleanliness?",
            "question_negative": "What did you not like about Cleanliness?",
            "options": {
    			"1": "Bathroom and toiletries",
    			"2": "Bed, cover and upholstery",
    			"3": "Kitchen",
    			"4": "Lobby, the interiors in general"
    		}
        },
        {
            "aspect": "Value For Money",
            "question_positive": "...",
            "question_negative": "...",
            "options": {}
        },
        {
            "aspect": "Rooms",
            "question_positive": "...",
            "question_negative": "...",
            "options": {}
        },
        {
            "aspect": "Service",
            "question_positive": "...",
            "question_negative": "...",
            "options": {}
        },
        {
            "aspect": "Location",
            "question_positive": "...",
            "question_negative": "...",
            "options": {}
        }
	]
}
```
- Difference here:
  - `units_question`, `units_placeholder_text` and `units` are absent.
  - `unit_name` is present instead of `business_name`.


#### **POST**

- When the user gives rating above threshold for all aspects:  
```json
{
	"rating": 5,
    "aspect_ratings": [
        {"aspect": "Cleanliness", "rating": 5},
        {"aspect": "Value For Money", "rating": 5},
        {"aspect": "Rooms", "rating": 5},
        {"aspect": "Service", "rating": 5},
        {"aspect": "Location", "rating": 5}
    ],
	"response_text": "",
	"respondent": {
		"name": "",
		"email": "",
		"phone": ""
	}
}
```

- When the user gives rating below threshold for some/all aspects:
```json
{
	"rating": 5,
	"response_text": "I really enjoyed my stay at Sterling Goa. The location was ...",
    "aspect_ratings": [
        {"aspect": "Cleanliness", "rating": 3},
        {"aspect": "Value For Money", "rating": 5},
        {"aspect": "Rooms", "rating": 5},
        {"aspect": "Service", "rating": 3},
        {"aspect": "Location", "rating": 5}
    ],
    "disliked_aspects": [
        {
            "aspect": "Cleanliness",
            "selected_options": {
                "1": "Bathroom and toiletries",
                "4": "Lobby, the interiors in general"
            }
        },
        {
            "aspect": "Service",
            "selected_options": {
                "3": "..."
            }
        }
	],
	"respondent": {
		"name": "Eddie Vedder",
		"email": "eddie_vedder@apple.com",
		"phone": "8989898989"
	}
}
```


**Notes for body of `POST` request:**  
* `rating`, `aspect_ratings`, `response_text` and `respondent` should always be present.
* Except `rating` and `aspect_ratings`, everything else may be blank (If the user gives rating above threshold for everything).
* If rating is above threshold, `disliked_aspects` can be omitted.
