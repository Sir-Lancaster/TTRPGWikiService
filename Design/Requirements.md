# Requirements

There should be two types of users: Admins, and Writers.

## Admin

An Admin user should be the owner of the Universe, as such they need some executive powers:
- Adding and inviting users.
  - How does this look? 
    - Share a private link to other users
    - Look up Users by User ID's/usernames
    - Writer requests access to a universe.
- Ability to view hidden documents (if they choose).
  - If a writer marks a document as invisible, the Admin should still be able to see the title of the document, and can choose to open the document and read it. 
  - A pop-up should appear, informing the admin that if it was hidden, it was probably done due to it being an important plot point to the owner of that world. The Admin should then be able to click `back` to go back to the document list, or `enter anyway` to view the document. 
- Kicking users.
  - In the unfortunate occurance where a user must be kicked to prevent damage from being done, the admin can at their discression remove the user from the universe.
  - The admin should then be able to choose to either assume ownership of their documents, or delete them. 
- Changing permissions of a user.
  - What are the permissions a user can have? See [Writer](#writer).
- Control over documents and worlds.
  - An admin should be able to delete documents and worlds when necessary. 
  - Change visibility of a world/document.
  - Create a world.
  - Add documents to a world.
- Changing attributes of a Universe (description, title, picture).
  - Basic categorization for a user who has multiple Universes. 
  - description of the universe.
  - Name of the universe.
  - Picture for the universe. 

## Writer

A Writer is a world smith. Given stewardship over a world, it is their responsibility to maintain, and write the lore and general worldbuilding. 
- Change the name, description, and picture of a world. 
- Upload images to be stored under their world.
  - Images might be used by markdown documents (if desired) or just maps for a continent that the players will be on when visiting their world.
- Create documents for their world.
  - Documents should be able to uploaded since most people will probably want to make their documents in their prefered editors. 
  - Documents should be editable from the webpage by users.  
- World Limit.
  - A writer should have a default limit of 1.
  - If a admin wants a user to help with world design for a larger scale game, then an admin should be able to change a users world limit (or set it to unlimited).
- Change visibility of documents created by them.
  - Able to hide documents that they don't want other writers to see. Admins are able to view the documents as described above.  
- Create a number of worlds equal to their world limit when on a univers' page.
  - When invited to a world, if an Admin has not created and assigned a writer to a world, the writer should just be able to create a world with them as the assigned writer. 
- Viewing privelages for other writers worlds. 
  - Hidden documents of other worlds should be entirely invisible to the visiting writer. 
- Permissions: 
  - Create a world.
  - Edit world details.
  - World limit, default 1.
  - document visibility configuration.
  - upload files.
  - edit files in their world.
  - create files in browser.
  - clone a worlds documents.

## User Stories

- A user should be able to create a new Universe. 
- A user should be able to create multiple worlds in a universe. 
- A user should be able to invite people to their universe. 
- A user who owns a universe, should be able to set permissions for the other users editting their universe.
- A user should be able to set and change visibility status of documents to keep mysteries from othe GM's
- A user should be able to upload images to their world or universe.
- A user should be able to upload documents to their world or universe. 
- A user should be able to edit documents in an online editor. 
- A user should be able to download or clone a universe to their device. 