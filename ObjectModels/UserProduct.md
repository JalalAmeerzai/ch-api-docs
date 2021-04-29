# UserProduct Object Model

## Overview

**Current version:** 1.0.1

**Logs:**

- 2020-07-10: **Added** `archived_course_data` to the `purchased_products` area.
- 2020-02-26: **Removed** defined product types in favor of imported product types from the product service.
- 2020-02-12: **Updated** the timestamp format to a standard format [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) / [RFC 3339](https://tools.ietf.org/html/rfc3339). **Updated** the `product_format` options. **Added** DB mappings for option names.
- 2019-12-10: **Changed** changed some types to be correct and updated the example.
- 2019-11-07: **Changed** `can_access` `bool` type to a string representation.
- 2019-10-11: **Added** `recent_product` to address the "resume bar" functionality needs.
- 2019-10-09: **Changed** `product_type` to `product_format` for consistency.
- 2019-10-08: **Modified** `media_metadata` again to fix gRPC incompatability.
- 2019-10-07: **Added** `last_accessed` to keep track of last tracks accessed by the user and **modified** `media_metadata` to include changes for individual tracks (also added their examples).
- 2019-09-05: Implemented the TODOs (added `purchase_date` and `registration_promo_item`), updated the structure of containing the objects to a map, and changed `name` to `title`.
- 2019-07-19: Changed `user_id` to `user_email` as the primary identifier (see [User Service](https://github.com/stdev/ch-user-service/blob/develop/user/User.md) for details).
- 2019-07-16: **Added** `subtitle` and `thumbnail_link`. **Changed** `updated_at` to `last_modified`. Fixed formatting of `purchased_products`.
- 2019-07-01: Fixed minor formatting.
- 2019-06-28: Created. Removed a confusing top level key for clarity.

## Purpose

The UserProduct Object Model is the representation of the user's purchased, digital products. It holds all the necessary data for those products in relation to the user and business needs.

## Definitions

- `user_email` _(string)_: This is an individual user's email (which is the unique primary key in the `user` table).
- `purchased_products` _(map of objects)_: A map of products owned by a user with the `sku` as a key and containing the following within an object:
	- `archived_course_data` _(string)_: a JSON string of legacy course data (notes, etc).
	- [DEPRECATED] `title` _(string)_: The name of the product.
	- [DEPRECATED] `subtitle` _(string)_: The subtitle of the product.
	- `purchase_date` _(string)_: The datetime of purchase for the product (e.g. `"2019-06-1T 00:00:00V"`)
	- [DEPRECATED] `thumbnail_link` _(string)_: The thumbnail image for the product.
	- `product_format` _(string)_: The type/format of a product containing the following (see [Product.format_type](Product.md)).
	- `can_access` _(string representation of bool)_: string representation of whether access to this product by this customer is valid. A use case for this is to deny access to a user's product when a payment (recurring payments) is missed.
	- `media_metadata` _(map)_: The metadata for media containing the following options (if applicable, for _each individual track_):
		- **[video/audio only]**
			- *KEY:* string representation of the individual audio track (or video) number (e.g. track 1's key would be `"1"`)
			- *VALUE:* string representation of last known video/audio time location (or video) (e.g. "00:31:16")
		- **[eBook only]**
			- `current_page` _(string)_:  last known page number _(**NOTE:** we might have to include other info, such as chapter, for the ebook)_.
	- `last_accessed` _(object)_: The track and datetime last accessed by the user (applies to video/audio types only).
		- `track` _(string)_: the string representation of an individual track number.
		- `access_time` _(string)_: string representation of when the track was last accessed UTC datetime (e.g. "2019-06-10T00:00:00Z").
	- `registration_promo_item` _(string representation of bool)_: Whether or not this product was given as a free product upon registration.
	- [DEPRECATED] `authors` _(list of strings)_: Authors for the product.
- `recent_product` _(string)_: The SKU of the product that was last interacted with by the user.
- `created_at` _(string)_: string representation of the initial user product entry (creation) [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) / [RFC 3339](https://tools.ietf.org/html/rfc3339) datetime (e.g. "2019-06-10T00:00:00Z").
- `last_modified` _(string)_: string representation of when the user product entry is updated [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) / [RFC 3339](https://tools.ietf.org/html/rfc3339) datetime (e.g. "2019-06-10T00:00:00Z").

### DEPRECATED FIELDS

The deprecated fields marked above _may_ still be included in the data retrieved. Please note that the data within these fields is **not reliable** nor **guaranteed accurate**! Currently, the fields _may_ or _may not_ exist BUT no _new data_ for these fields is being set.

The examples in these docs, for now, still include these fields. Again, **do not** rely on the data within these deprecated fields!

## JSON Representation (with example values) (minus comments)

_**NOTE:** The following is a representation of a **single** UserProduct object._
```json
{
	"user_email": "johndoe@example.com",
	"purchased_products": {
		"CE05948": {
            "title": "The Untethered Soul at Work: CE Credits",
            "subtitle": "",
            "purchase_date": "2019-06-10T00:00:00Z",
            "thumbnail_link": "somelocation/thumbnail.jpg",
            "product_format": "FORMAT_CE_CREDITS",
            "can_access": "true",
            "registration_promo_item": "true",
            "authors": [
                "John O'\''Donohue"
            ]
        },
		"AF02384W": {
            "title": "Longing and Belonging: Audio Download",
            "subtitle": "",
            "purchase_date": "2019-06-10T00:00:00Z",
            "thumbnail_link": "somelocation/thumbnail.jpg",
            "product_format": "FORMAT_AUDIO",
            "can_access": "true",
            "media_metadata": {
                "1": "00:16:23"
            },
            "registration_promo_item": "false",
            "authors": [
                "Author 1"
            ]
        },
        "VID123D": {
            "title": "Some Video (D)",
            "subtitle": "",
            "purchase_date": "2019-06-10T00:00:00Z",
            "thumbnail_link": "somelocation/thumbnail.jpg",
            "product_format": "FORMAT_VIDEO",
            "can_access": "true",
            "media_metadata": {
                "1": "01:21:49"
            },
            "registration_promo_item": "false",
            "authors": [
                "Author 2"
            ]
        },
        "VID123E": {
            "title": "Some Video (E)",
            "subtitle": "",
            "purchase_date": "2019-06-10T00:00:00Z",
            "thumbnail_link": "somelocation/thumbnail.jpg",
            "product_format": "FORMAT_VIDEO",
            "can_access": "true",
            "media_metadata": {
                "1": "01:21:49"
            },
            "registration_promo_item": "false"
        },
        "VID123F": {
            "title": "Some Video (F)",
            "subtitle": "",
            "purchase_date": "2019-06-10T00:00:00Z",
            "thumbnail_link": "somelocation/thumbnail.jpg",
            "product_format": "FORMAT_VIDEO",
            "can_access": "true",
            "media_metadata": {
                "1": "01:21:49"
            },
            "registration_promo_item": "false"
        },
        "BK05619W": {
            "title": "Making Magic: eBook",
            "subtitle": "",
            "purchase_date": "2019-06-10T00:00:00Z",
            "thumbnail_link": "somelocation/thumbnail.jpg",
            "product_format": "FORMAT_EBOOK",
            "can_access": "true",
            "media_metadata": {
                "current_page": "16"
            },
            "registration_promo_item": "false",
            "authors": [
                "Author 3",
                "Author 4"
            ]
        },
        "WC05064Y": {
                "authors": [
                    "Chris Germer",
                    "Kristin Neff"
                ],
                "media_metadata": {},
                "title": "The Power of Self-Compassion - Online Learning",
                "subtitle": "A Step-by-Step Training to Bring Kindness and Inner Strength to Any Moment of Your Life",
                "purchase_date": "2020-05-01T00:00:00Z",
                "thumbnail_link": "https://cdn.shopify.com/s/files/1/0253/2822/2307/products/PSC-shop.png?v=1590015402",
                "product_format": "FORMAT_COURSE",
                "can_access": "true",
                "last_accessed": {
                    "track": "1",
                    "access_time": ""
                },
                "registration_promo_item": "false",
                "archived_course_data":"{\"introduction\":{\"question-771\":{\"question\":\" What is your greatest wish for yourself after completing \<em\>The Power of Self-Compassion?\</em\>,\"},\"question-772\":{\"question\":\" What drew you to signing up for \<em\>The Power of Self-Compassion?\</em\>,\"},\"question-780\":{\"question\":\" Self-Judgment:,\"},\"question-781\":{\"question\":\" Common Humanity:,\"},\"question-782\":{\"question\":\" Isolation:,\"},\"question-783\":{\"question\":\" Mindfulness:,\"},\"question-784\":{\"question\":\" Over-Identification:,\"},\"question-785\":{\"question\":\" Overall Score:\"},\"question-789\":{\"question\":\" Self-Kindness:,\"},\"question-808\":{\"question\":\" What would your life look like if you were more self-compassionate?,\"}},\"journal\":{\"session 1\":{\"page 1\":{\"comment 1\":[\"This is a note on session 1. Hello world.\",\"1527018973\"]}},\"session 2\":{\"page 1\":{\"comment 2\":[\"session 2 entry\",\"1527019015\"]}}},\"workbook\":{\"1: What Is Self-Compassion?\":{\"question-1179\":{\"answer\":\"sldkfj sdlkfj kldf\",\"question\":\" Think about how you typically treat a good friend when they are struggling.  What types of things do you say, what is your tone of voice, and what is your stance toward your friend?,\"},\"question-1180\":{\"answer\":\"sdlfk sdlkfj slkdfj ksldfj d\",\"question\":\" Think about how you typically treat yourself when you are struggling? What types of things do you say, what is your tone of voice, and what is your stance toward yourself?,\"},\"question-1181\":{\"answer\":\"dsf sdf sdf sf sf\",\"question\":\" Do you notice any patterns of difference between how you treat yourself and how you treat your friends in moments of struggle?,\"},\"question-726\":{\"question\":\" Sometimes it takes a while to find a touch that feels comfortable, but once you find one it’s one of the easiest and quickest ways to give yourself compassion in a moment of emotional struggle. Were you able to find a soothing touch that felt supportive and comforting? If not, would you be willing to continue that exploration?,\"},\"question-727\":{\"question\":\" What was the self-compassion break like for you?,\"},\"question-728\":{\"question\":\" Did you notice anything after saying the first phase, “This is a moment of suffering”? Any shifts?,\"},\"question-729\":{\"question\":\" How about the second phrase, which reminded you that situations like this were part of being human? Was there a change for you?,\"},\"question-730\":{\"question\":\" How did it go after the third phrase when you were invited to be kind to yourself? Were you able to speak heart to heart with a friend? What was it like to say the same words to yourself? Was it easier? Was it more difficult?\"}},\"2: The Foundation of Mindfulness\":{\"question-717\":{\"question\":\" If you are familiar with breath meditation, how was it to bring affection and appreciation into the practice, to allow yourself to be soothed by your own breath? Were you able to\<em\> be\</em\> the breath?,\"},\"question-718\":{\"question\":\" Did you notice that your attention increased when you \<em\>enjoyed\</em\> the breath?,\"},\"question-719\":{\"question\":\" Could you feel the difference between “being”—being held by the breath—and “doing”—trying to keep your attention with the breath?,\"},\"question-720\":{\"question\":\" Write down any additional comments or observations you experienced during the Affectionate Breathing Meditation.,\"},\"question-721\":{\"question\":\" Write down a current situation in your life where you feel that resisting the reality of something painful is causing you unnecessary suffering, and may actually be making things worse than they need to be (e.g., procrastinating on a big project, resenting something about your current job, harboring anger toward your neighbor’s barking dog).,\"},\"question-722\":{\"question\":\" How do you know that you are resisting? Is there any discomfort in the body or the mind? Can you describe it?,\"},\"question-723\":{\"question\":\" What are the consequences of resisting? For example, how might your life be easier if you stopped resisting or resisted a little less?,\"},\"question-724\":{\"question\":\" Can you see that resistance might be serving you in some way? Perhaps resistance is helping you not to feel certain feelings? If difficult feelings arise, please be kind to yourself. Honor your resistance, knowing that it allows you to function in the world.,\"},\"question-725\":{\"question\":\" Now, please consider how mindfulness or self-compassion might help lessen your resistance in this situation. Might validating the pain (“This is tough”) and letting it into your life (“opening the hand of fear”) make things easier or more difficult? Or would offering yourself a little forgiveness (“It’s not your fault”) or understanding (“This is how people feel in these situations”) bring some relief?,\"},\"question-767\":{\"question\":\" During the Informal Practice: Soles of the Feet, what did you notice when you anchored your attention in your feet? What did you not notice?,\"},\"question-768\":{\"question\":\" How did anchoring your attention in your feet impact your mental and emotional state?,\"},\"question-769\":{\"question\":\" How might you actually use this in your daily life (e.g., walking to your car, at the grocery store, cleaning the house)?\"},\"question-770\":{\"question\":\" What did you notice during the Affectionate Breathing Meditation? What did you feel? How are you feeling now?,\"}},\"3: Lovingkindness\":{\"question-1216\":{\"question\":\" What do I need?,\"},\"question-1217\":{\"question\":\" How might I phrase my need as a wish for myself?,\"},\"question-1218\":{\"question\":\" What do I need to hear from others?,\"},\"question-1219\":{\"question\":\" How might I phrase what I need to hear as a wish for myself?,\"},\"question-1220\":{\"question\":\" Select one to three words or phrases that you would like to use in lovingkindness meditation.,\"},\"question-760\":{\"question\":\" Were you able to settle on a loved one? How did that feel for you?,\"},\"question-761\":{\"question\":\" Were there any challenging aspects to this meditation for you? Could you hold \<em\>those\</em\> in compassion?,\"},\"question-762\":{\"question\":\" Did you find that the phrases weren’t really evoking feelings of lovingkindness?,\"},\"question-763\":{\"question\":\" What did you notice? What came up for you? What was the process like for you?,\"},\"question-764\":{\"question\":\" How do we know we found a good phrase? With gratitude there is no more longing; we are complete. Did you find any phrases that filled your heart with gratitude?,\"},\"question-765\":{\"question\":\" What words or phrases did you write down during the Finding Lovingkindness Phrases practice?\"},\"question-766\":{\"question\":\" What did you notice? What came up for you? What was your experience?,\"}},\"4: Finding Our Inner Compassionate Voice\":{\"question-1238\":{\"question\":\" Write down a behavior that you would like to change that you typically criticize yourself for.,\"},\"question-1239\":{\"question\":\" Please write down what you typically say to yourself when you find yourself engaging in this behavior.,\"},\"question-1240\":{\"question\":\" After getting in touch with the part of you that feels criticized, notice how it feels to receive this message. What has been the impact on you?,\"},\"question-1241\":{\"question\":\" Please write some words of kindness to yourself for how hard it has been to hear such criticism.,\"},\"question-1242\":{\"question\":\" Now ask yourself what might be driving your inner critic? Is it trying to keep you safe in some way, even if that hasn’t been the end result? What motivates your inner critic?,\"},\"question-1243\":{\"question\":\" If you were able to connect in some way with the way the inner critic is attempting to help you, try writing a few words of thanks to your inner critic.,\"},\"question-1244\":{\"question\":\" After getting in touch with your inner compassionate self, write some words of encouragement and support about changing this behavior from your inner compassionate self.,\"},\"question-745\":{\"question\":\" What was it like to whisper the words into your ear? Were you able to let them in?,\"},\"question-746\":{\"question\":\" Were you able to let in and hear the words from the inside?,\"},\"question-747\":{\"question\":\" The phrases for self-compassion sometimes differ a little from lovingkindness. Compassion phrases reflect compassion as a loving response to suffering rather than as simply encouragement to be happy. Did this meditation differ from Lovingkindness for a Loved One?,\"},\"question-748\":{\"question\":\" Where are you now in this program in terms of the stages of progress? Please take a moment to reflect on what part of the cycle you might be in right now—striving, disillusionment, or true acceptance?,\"},\"question-749\":{\"question\":\" How is the program going for you now? Surveying the terrain of your heart, has there been anything particularly surprising, challenging, uplifting, or confusing so far in the course?,\"},\"question-750\":{\"question\":\" If you are struggling, is there any way that you can reduce the struggle through mindfulness? Is there any experience that you would like to give a little more space—or let go of—that would make your participation in the program easier or more delightful?,\"},\"question-751\":{\"question\":\" We all have difficult parts of ourselves—parts we have trouble accepting with compassion. Is there any part of yourself that you might be beginning to embrace or befriend, or that you would like to learn to embrace or befriend?,\"},\"question-752\":{\"question\":\" Could you identify the voice of inner criticism?,\"},\"question-753\":{\"question\":\" How did it feel to consider the part of you that feels criticized and give yourself some compassion?,\"},\"question-754\":{\"question\":\" Were you able to find any way your critical voice was trying to help you?,\"},\"question-755\":{\"question\":\" Did it make sense to thank the inner critic for its efforts?,\"},\"question-756\":{\"question\":\" Were you able to get in contact with your inner compassionate self?,\"},\"question-757\":{\"question\":\" What was the impact of saying the words, “I love you and don’t want you to suffer”?,\"},\"question-758\":{\"question\":\" Could you write from the perspective of your inner compassionate voice? If so, how did it feel to relate to this behavior that you have been trying to change for so long? If you weren’t able to get in touch with yourself, were you able to have compassion for that, too?,\"},\"question-759\":{\"question\":\" How are you feeling now? Is there anything you need in the moment?\"}},\"5: Living a Meaningful Life\":{\"question-1225\":{\"question\":\" Please write down what happened – a struggle in your life that seemed very difficult to bear at the time, but looking back, taught you an important lesson.,\"},\"question-1226\":{\"question\":\" What deeper lesson did the challenge or crisis teach you that you might not otherwise have learned?,\"},\"question-1227\":{\"question\":\" Is there a \<em\>current situation\</em\> in your life right now that might have a silver lining—a hidden message. If so, what is that hidden lesson?,\"},\"question-1228\":{\"question\":\" Is there any way that you can use mindfulness or self-compassion to be with this current situation in your life that might make you feel safer and allow you to learn from it?,\"},\"question-1229\":{\"question\":\" Imagining that you are near the end of your life, what values did your life embody that gave your life meaning and satisfaction?,\"},\"question-1230\":{\"question\":\" Please write down any ways that you might not be living in accord with your core values. If there are many, please choose just one.,\"},\"question-1231\":{\"question\":\" Are there any \<em\>external obstacles\</em\> that might be getting in the way of living in accord with your core values?,\"},\"question-1232\":{\"question\":\" Are there any \<em\>internal\</em\> obstacles?,\"},\"question-1233\":{\"question\":\" How might self-kindness and self-compassion help you live in accord with your core values?,\"},\"question-1234\":{\"question\":\" What core value (or values) are so important to you that you would like to remind yourself of it every day?,\"},\"question-1235\":{\"question\":\" How could you frame the core value in the form of a vow, perhaps adding “May I…”?,\"},\"question-1236\":{\"question\":\" How did it feel to affirm your core value in the form of a vow?,\"},\"question-1237\":{\"question\":\" Is there a daily ritual you might like to establish to keep your vow alive in your life?\"},\"question-737\":{\"question\":\" What did you notice during the meditation? What did you feel?,\"},\"question-738\":{\"question\":\" Was it easier to breathe compassion in for yourself or out for another?,\"},\"question-739\":{\"question\":\" Were you able to adjust the flow depending on the needs of the moment?,\"},\"question-740\":{\"question\":\" How did it feel to notice and validate the internal and external obstacles?,\"},\"question-741\":{\"question\":\" How was it to bring kindness and compassion to yourself in the face of those obstacles?,\"},\"question-742\":{\"question\":\" How was it to forgive yourself for being imperfect?,\"}},\"6: Working with Difficult Emotions\":{\"question-736\":{\"question\":\" Were you able to find a label for the difficult emotion? What happened for you when you labeled it? Did you notice any shift?,\"},\"question-801\":{\"question\":\" Did you encounter any difficulties with this practice?\"},\"question-802\":{\"question\":\" Did the emotion change when you softened that part of the body, soothed yourself, and allowed it to be there?,\"},\"question-803\":{\"question\":\" What was it like to explore your body for the physical sensation associated with the emotion?,\"}},\"7: Self-Compassion in Relationships\":{\"question-790\":{\"question\":\" Did you hear something meaningful for this time in your life?,\"},\"question-791\":{\"question\":\" Did you have anything you wanted to say to this being?,\"},\"question-792\":{\"question\":\" Did you receive anything with special meaning?,\"},\"question-793\":{\"question\":\" What was it like to discover that this warm, compassionate friend is actually a part of you, accessible whenever you need it?,\"},\"question-794\":{\"question\":\" What did you notice? What did you feel?,\"},\"question-795\":{\"question\":\" Did you notice a shift when you said the equanimity phrases?,\"},\"question-796\":{\"question\":\" What was it like to breathe in for yourself and out for another? What was it like to relate to that other person?,\"},\"question-797\":{\"question\":\" Were you able to use the breath to send and receive compassion?,\"},\"question-798\":{\"question\":\" Could you adjust the flow so more went to yourself or the other as needed?\"},\"question-799\":{\"question\":\" Could you visualize a compassionate friend or have a sense of presence?,\"},\"question-800\":{\"question\":\" Could you visualize a reasonably safe and peaceful place?,\"}},\"8: Embracing Your Life and Yourself\":{\"question-1279\":{\"question\":\" Please write down five deeply meaningful and important things that you are grateful for in your life.,\"},\"question-1280\":{\"question\":\" Now please write down five small and insignificant things that you are also grateful for.,\"},\"question-1281\":{\"question\":\" Reflect on how it feels inside when you open your heart in gratitude, and make any notes you wish.,\"},\"question-1283\":{\"question\":\" What are three to four things that you really like and appreciate about yourself?,\"},\"question-1284\":{\"question\":\" Going even deeper down, what else do you appreciate about yourself?,\"},\"question-1285\":{\"question\":\" Please choose one good quality and note down anyone who may have helped you develop that good quality.,\"},\"question-1289\":{\"question\":\" Heart Question: “What touched me, moved me, or shifted inside of me?” Please jot down what arises for you.,\"},\"question-1290\":{\"question\":\" Practice Question: “What practices worked for me?” Please note down the practices that were most interesting, enjoyable, or meaningful to you.,\"},\"question-773\":{\"question\":\" What did you notice? What did you feel?,\"},\"question-774\":{\"question\":\" What was it like to give compassion to whomever came into your mind’s eye? Did anything surprise you? Is this a practice you would like to remember?,\"},\"question-775\":{\"question\":\" What would your life look like if you practiced this before you left your home each morning?,\"},\"question-776\":{\"question\":\" What was that like for you? Were you able to think of qualities you liked about yourself?,\"},\"question-777\":{\"question\":\" How did it make you feel—good or uncomfortable?,\"},\"question-778\":{\"question\":\" Did self-appreciation become easier when you brought in gratitude for others?,\"},\"question-779\":{\"question\":\" How did you feel at the end of the exercise?,\"},\"question-780\":{\"question\":\" Self-Judgment:,\"},\"question-781\":{\"question\":\" Common Humanity:,\"},\"question-782\":{\"question\":\" Isolation:,\"},\"question-783\":{\"question\":\" Mindfulness:,\"},\"question-784\":{\"question\":\" Over-Identification:,\"},\"question-785\":{\"question\":\" Overall Score:,\"},\"question-786\":{\"question\":\" Now, go back and compare your self-compassion quiz scores from the beginning of the course to your scores from the end of the course. What did you notice about your scores from the beginning of the course to the end of the course?,\"},\"question-787\":{\"question\":\" Overall, are you more self-compassionate with yourself after taking this course?,\"},\"question-788\":{\"question\":\" What would your life look like if you continued to practice self-compassion?\"},\"question-789\":{\"question\":\" Self-Kindness:,\"}}}}"
            },
	},
	"recent_product": "AF02384W",
	"created_at": "2019-06-10T00:00:00Z",
	"last_modified": "2019-06-10T00:00:00Z"
}
```