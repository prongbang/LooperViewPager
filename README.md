# Looper View Pager
The Simple Looper View Pager Android Library

## Download
> build.gradle
```gradle
allprojects {
    repositories {
        ...
        maven { url 'https://jitpack.io' }
    }
}

...

dependencies {
    implementation 'com.github.prongbang:looperviewpager:1.0.0'
}
```

## How to use
> MainActivity.kt
```kotlin

override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
    setContentView(R.layout.activity_main)

    val galleries = ArrayList<Gallery>()
    galleries.add(Gallery(1, "Gallery 1",""))
    galleries.add(Gallery(2, "Gallery 2",""))
    galleries.add(Gallery(3, "Gallery 3",""))
    galleries.add(Gallery(4, "Gallery 4",""))
    galleries.add(Gallery(5, "Gallery 5",""))

    val adapter = GalleryPagerAdapter(supportFragmentManager, vpGallery)
    adapter.setItems(galleries)
    vpGallery.adapter = adapter
    vpGallery.addOnPageChangeListener(adapter)
    vpGallery.setCurrentItem(1, false)

    vpIndicator.setupWithViewPager(vpGallery, galleries.size)

}

override fun onDestroy() {
    super.onDestroy()
    vpIndicator.cleared()
}
```

> GalleryPagerAdapter.kt
```kotlin
class GalleryPagerAdapter(fm: FragmentManager, viewPager: ViewPager) : LooperFragmentStatePagerAdapter<Gallery>(fm, viewPager) {

    override fun getItem(oldPosition: Int, newPosition: Int): Fragment {

        return GalleryFragment.newInstance(content[newPosition])
    }

}
```

> Gallery.kt
```kotlin
import android.os.Parcel
import android.os.Parcelable

data class Gallery(
        var id: Int,
        var name: String,
        var image: String
) : Parcelable {

    constructor() : this(0, "", "")

    constructor(source: Parcel) : this(
            source.readInt(),
            source.readString(),
            source.readString()
    )

    override fun describeContents() = 0

    override fun writeToParcel(dest: Parcel, flags: Int) = with(dest) {
        writeInt(id)
        writeString(name)
        writeString(image)
    }

    companion object {
        @JvmField
        val CREATOR: Parcelable.Creator<Gallery> = object : Parcelable.Creator<Gallery> {
            override fun createFromParcel(source: Parcel): Gallery = Gallery(source)
            override fun newArray(size: Int): Array<Gallery?> = arrayOfNulls(size)
        }
    }
}
```

> GalleryFragment.kt
```kotlin
class GalleryFragment : Fragment() {

    companion object {

        @JvmStatic
        fun newInstance(gallery: Gallery): GalleryFragment {
            val fragment = GalleryFragment()
            val bundle = Bundle()
            bundle.putParcelable(Gallery::class.java.simpleName, gallery)
            fragment.arguments = bundle
            return fragment
        }
    }

    override fun onCreateView(inflater: LayoutInflater, container: ViewGroup?, savedInstanceState: Bundle?): View? {
        // Inflate the layout for this fragment
        val v = inflater.inflate(R.layout.fragment_gallery, container, false)

        if (arguments != null) {
            val gallery = arguments!!.getParcelable<Gallery>(Gallery::class.java.simpleName)

            val tvMessage = v.findViewById<TextView>(R.id.tvMessage)
            tvMessage.text = gallery.name
        }

        return v
    }

}
```

### Thank