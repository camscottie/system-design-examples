# Introduction
This project is inspired by a company that connects mental health practitioners to those seeking their services, including students, NHS patients and private insurance clients.

Here I lay out the pre-requisites for a data system to match mental health practitioners to students seeking their services. In this page I consider a user's data journey and requiremenst around privacy, security and governance.

# What Does the User Want?
When we process data, it's important to consider the experience that we are trying to provide to our downstream customers. As a data engineer, it is tempting to think only of the people who we will hand off the data to such as data analysts or machine learning scientists, but it's important to consider how the system affects the final user experience.

## User Journey
Students will be prompted through the following steps to be matched to counsellors:

- **Register:** This is the initial sign-up to the website, where name and email address is requested and validated.
- **Funding:** Choose between self-funded, insurance, university or NHS
- **Connect Institutions:** user requests access to records, or provides any references directly (e.g. GP referral note, insurance reference)
- **Match Student to Counsellors:** This is where students can take a survey, consider the types of services that they may be looking for and provide preferences to find counsellors that match their requirements. 


## Funding Options
One important thing that will change the data needed per person is where the funding is coming from. There are four possible payment pathways:
1. Self-funded
2. Funded by private insurance
3. Funded by the university
4. Funded by the NHS

Each option comes with it's own set of considerations around what data is essential to provide matching. 

**Self-funding:** The student could be directed by the university directly to platform provider's website. In this case client information can be directly input to the platform and no connection to the university or their GP is required.

**Funded by private insurance**: Medical private insurance companies will provide a reference code, so validation of this reference is a requirement for the insurance company to provide payment.

**Funding by university:** The university could have a budget that they can allow students to make use of. Therefore it's important to know if a student requesting the service has been given permission to use this budgeted amount.

**Funding by the NHS:** A student will need a GP referral in order to get access to counsellors that are listed by the NHS. To abide by strict data protection, data fields are limited to those required by the service such as referral document, practice details, and patient name. 

## Governance, Privacy, Security 
In this application, considerations for Personally Identifiable Information(PII) is a necessity. Information about those seeking a counselling service need to be kept between counsellor and client, and a breach to this could have negative consequences on a large amount of people when designing systems at scale.
### Governance - Standards and Regulations
The Care Quality Commission lays out guidance on their website that follows rules set by the General Data Protection Regulation (GDPR) and the National Data Guardian's 10 data security standards. [1]

Additionally NHS England lays out what data patients have access to and how patients have control over this. [2],  [3] 

In creating the data model, we need to consider the type of institution the information is coming from (private insurance, NHS or University), what data is needed to achieve the recommendation service goals, and the security and privacy measures needed to ensure data is only accessed by the right people.

### Privacy
To store data in a way that allows only the right people to view it, we need to consider privacy measures such as encryption, masking and pseudonymisation.
#### Encryption
Because we want to assign different roles access the database in different ways, encryption may be a suitable option. For example, a practitioner will be able to access columns such as patient name and a description of the students symptoms, alongside information on what the payment method is. Students may need access to similar details, but they could have additional information about themselves such as the name of their GP practice, and home location 

All connections to the database will be limited by a user account process, and data accessed is subject to encryption across the network.
#### Masking
Masking transforms sensitive information to avoid directly exposing original plaintext values.
Properly masked data provides utility for testing systems and investigating issues without  compromising security or privacy. A field like the insurance reference would be a good candidate for masking.
#### Pseudonymisation
Pseudonymising certain fields like a student's first name means that this information can be linked to a table, without giving away the sensitive information. This is a good way to acccess the data without exposing it's value, similar to the use of masking. However it has to be used with caution - this still links the data to the original exposed information, so precaution needs to be taken that the original source is not accessible to those other than database admins. [4]
### Security
Role-based access is a way that data can be protected.
Roles we consider in this data pipeline:
- Counsellors
- Students
- Data Admins/Engineers
- Data Scientists/Analysts

*Counsellors* will have access to the necessary PII that allows them to connect with the students that are using their services.

*Students* will have access to the necessary information about practitioners, so they can make an informed choice.

*Data Admins / Engineers* will have super-user access, as they control the access requirements for other roles, alongside ensure data consistency and reliable access to the data.

*Data Scientists/Analysts* may have access to masked / pseudonymised versions of the information, so that they can gather insights from aggregates and broad statistical grouping.

## Summary 
Before making technical system design choices, laying out the user experience and the ways that their information could be accessed and compromised is key to ensuring the safety of the individual users. It helps engineers to scope out the requirements of their system designs with more clarity, putting the focus on the people who will be using it, rather than solely on those building it.
* [1] https://www.cqc.org.uk/guidance-providers/all-services/check-way-you-handle-personal-information-meets-right-standards-0
* [2] https://digital.nhs.uk/your-data/understanding-data-in-health-and-care
* [3] https://digital.nhs.uk/data-and-information/keeping-data-safe-and-benefitting-the-public
* [4] https://ico.org.uk/for-organisations/uk-gdpr-guidance-and-resources/data-sharing/anonymisation/pseudonymisation/#whatispseudonymisation
