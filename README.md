# Descargar-imagen-en-IOS-Swift


# Synchronously:

```
if let filePath = Bundle.main.path(forResource: "imageName", ofType: "jpg"), let image = UIImage(contentsOfFile: filePath) {
    imageView.contentMode = .scaleAspectFit
    imageView.image = image
}

```

# Asynchronously:

  - Create a method with a completion handler to get the image data from your url
  
  ```
  
  func getDataFromUrl(url: URL, completion: @escaping (Data?, URLResponse?, Error?) -> ()) {
    URLSession.shared.dataTask(with: url) { data, response, error in
        completion(data, response, error)
    }.resume()
  }
  
  ```
  
  - Create a method to download the image (start the task)

  ```
  func downloadImage(url: URL) {
    print("Download Started")
    getDataFromUrl(url: url) { data, response, error in
        guard let data = data, error == nil else { return }
        print(response?.suggestedFilename ?? url.lastPathComponent)
        print("Download Finished")
        DispatchQueue.main.async() {
            self.imageView.image = UIImage(data: data)
        }
    }
  }
  
  ```
  
  - Usage:
  
  ```
  override func viewDidLoad() {
    super.viewDidLoad()
    // Do any additional setup after loading the view, typically from a nib.
    print("Begin of code")
    if let url = URL(string: "http://www.apple.com/euro/ios/ios8/a/generic/images/og.png") {
        imageView.contentMode = .scaleAspectFit
        downloadImage(url: url)
    }
    print("End of code. The image will continue downloading in the background and it will be loaded when it ends.")
  }
  ```
  
  # Extension
  
  ```
  extension UIImageView {
    func downloadedFrom(url: URL, contentMode mode: UIViewContentMode = .scaleAspectFit) {
        contentMode = mode
        URLSession.shared.dataTask(with: url) { data, response, error in
            guard
                let httpURLResponse = response as? HTTPURLResponse, httpURLResponse.statusCode == 200,
                let mimeType = response?.mimeType, mimeType.hasPrefix("image"),
                let data = data, error == nil,
                let image = UIImage(data: data)
            else { return }
            DispatchQueue.main.async() { 
                self.image = image
            }
        }.resume()
    }
    func downloadedFrom(link: String, contentMode mode: UIViewContentMode = .scaleAspectFit) {
        guard let url = URL(string: link) else { return }
        downloadedFrom(url: url, contentMode: mode)
    }
  }
  ```
  
  - Usage: 
  
  ```
  imageView.downloadedFrom(link: "IMAGE URL")
  ```

