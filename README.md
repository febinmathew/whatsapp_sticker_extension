# Extending WhatsApp Stickers support for AroundMe App

**This repo gives the idea as well as codes about how to extend WhatsApp stickers support for other platforms as well. In this tutorial we will be using AroundMe App as an example. but this would work for other Apps as well.**

The resources in this  All the resources that are used for this tutorials are available in this repo. You can clon it into your system if required or feel free to use the one you like. 

*This tutorial uses AroundMe App for Strickers extension. You can download AroundMe App from [here](https://play.google.com/store/apps/details?id=com.hash.aroundme).*

---

### 1. Edit **```activity_sticker_pack_details.xml```** (res/layout)

Just after/before the **"ADD TO WHATSAPP"** button view, Insert the following code snippet. (Just before the last line. If you find it hard to locate).

    <FrameLayout
        android:layout_width="match_parent"
        android:layout_height="60dp"
        android:layout_marginStart="@dimen/preview_side_margin"
        android:layout_marginLeft="@dimen/preview_side_margin"
        android:layout_marginEnd="@dimen/preview_side_margin"
        android:layout_marginRight="@dimen/preview_side_margin">
        <TextView
            android:id="@+id/already_added_text_aroundme"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_gravity="center"
            android:gravity="center"
            android:text="Sticker pack added to AroundMe"
            android:textColor="#9B9B9B"
            android:textSize="14sp"
            android:visibility="gone"
            tools:visibility="visible" />
        <FrameLayout
            android:id="@+id/add_to_aroundme_button"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_gravity="bottom"
            android:background="@drawable/btn_green"
            android:backgroundTint="#1b6db0"
            android:clickable="true"
            android:focusable="true"
            android:foreground="?attr/selectableItemBackgroundBorderless">
            <LinearLayout
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:orientation="horizontal"
                android:gravity="center">
                <ImageView
                    android:tint="#ffffff"
                    android:layout_width="30dp"
                    android:layout_height="30dp"
                    android:src="@drawable/sticker_3rdparty_am"/>
                <TextView
                    style="@style/StickerPreviewButtonText"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:textAllCaps="true"
                    android:background="@android:color/transparent"
                    android:clickable="false"
                    android:drawablePadding="8dp"
                    android:focusable="false"
                    android:foreground="@android:color/transparent"
                    android:text="Add to AroundMe" />
            </LinearLayout>
        </FrameLayout>
    </FrameLayout>
    
This would create another button that says **"ADD TO AROUNDME"**. *(Kindly explore the repo for **"sticker_3rdparty_am"** drawable)*.

### 2. Edit **StickerPackDetailsActivity.java**
Add the following lines of code inside ```onCreate``` function of the ```StickerPackDetailsActivity``` class.

    View addButton_AroundMe = findViewById(R.id.add_to_aroundme_button);
    addButton_AroundMe.setOnClickListener(v -> addStickerPackToAroundMe(stickerPack.identifier, stickerPack.name));

### 3. Edit **AddStickerPackActivity.java**
Add the following code snippet anywhere inside the ```AddStickerPackActivity``` class.

    public static String AROUNDME_PACKAGE_NAME="com.hash.aroundme";
    protected void addStickerPackToAroundMe(String identifier, String stickerPackName) {
        Intent intent = createIntentToAddStickerPack(identifier, stickerPackName);
        intent.setPackage(AROUNDME_PACKAGE_NAME);
        intent.setAction("com.hash.aroundme.intent.action.ENABLE_STICKER_PACK");
        try {
            startActivityForResult(intent, ADD_PACK);
        } catch (ActivityNotFoundException e) {
            Toast.makeText(this, "Sticker pack not added. If you'd like to add it, make sure you update to the latest version of    AroundMe.", Toast.LENGTH_LONG).show();
        }
    }

### 4. Edit **sticker_packs_list_item.xml** (res/layout)
Replace the **"+"** button view code with the following code block. (If you find it hard to find, simply search for the view with id ```add_button_on_list```).

     <ImageView
        android:id="@+id/add_button_on_list"
        android:layout_width="48dp"
        android:layout_height="48dp"
        android:layout_marginStart="8dp"
        android:layout_marginLeft="8dp"
        android:layout_marginTop="8dp"
        android:layout_marginBottom="8dp"
        android:background="?attr/selectableItemBackground"
        android:contentDescription="@string/add_button_content_description"
        android:scaleType="center"
        android:src="@drawable/sticker_3rdparty_add"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toStartOf="@id/add_button_on_list_aroundme"
        app:layout_constraintTop_toTopOf="parent" />

        <ImageView
            android:background="?attr/selectableItemBackground"
            android:layout_marginStart="8dp"
            android:layout_marginLeft="8dp"
            android:layout_marginTop="8dp"
            android:layout_marginBottom="8dp"
            android:id="@+id/add_button_on_list_aroundme"
            app:layout_constraintBottom_toBottomOf="parent"
            app:layout_constraintEnd_toEndOf="parent"
            app:layout_constraintTop_toTopOf="parent"
            android:layout_width="48dp"
            android:layout_height="48dp"
            android:layout_gravity="center"
            android:adjustViewBounds="true"
            android:padding="15dp"
            android:contentDescription="@string/add_button_content_description"
            android:src="@drawable/sticker_3rdparty_am"
            />
    
### 5. Edit **StickerPackListItemViewHolder.java**
Declare a variable for ImageView anywhere inside the ```StickerPackListItemViewHolder``` class.

    final ImageView addButtonAroundMe;
    
Add the following code inside the ```StickerPackListItemViewHolder``` constructor.

    addButtonAroundMe = itemView.findViewById(R.id.add_button_on_list_aroundme);
    
### 6. Edit **StickerPackListAdapter.java**
Go to ```StickerPackListAdapter``` class and add paste the below code block inside the ```onBindViewHolder``` function (at the end of the function),

    viewHolder.addButtonAroundMe.setOnClickListener(v -> {
            if(aroundMeAddButtonClickListener!=null)aroundMeAddButtonClickListener.onAddButtonClicked(pack);
        });

...Add the following code anywhere inside ```StickerPackListAdapter``` class.

    public void setAroundMeAddButtonClickListener(OnAddButtonClickedListener aroundMeAddButtonClickListener) {
        this.aroundMeAddButtonClickListener = aroundMeAddButtonClickListener;
    }

    private OnAddButtonClickedListener aroundMeAddButtonClickListener;
    
    
### 7. Edit **StickerPackListActivity.java**

Inside the ```showStickerPackList``` fucntion just after initialization of ```allStickerPacksListAdapter```  *(allStickerPacksListAdapter = new StickerPackListAdapter(stickerPackList, onAddButtonClickedListener);)* object, Add the following code.


    allStickerPacksListAdapter.setAroundMeAddButtonClickListener(pack-> addStickerPackToAroundMe(pack.identifier, pack.name));    
