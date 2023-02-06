# swift_create_your_own_typeeraser

```swift

import UIKit
import Foundation

// 1. CREATE PROTOCL WITH ASSOCIATED TYPE
protocol ModelLoading {
    associatedtype Model

    func load(completionHandler: (Result<Model, Error>) -> Void) -> Void
}

struct MyModel {
   
    
}

// 2. CREATE `ANY`+NAME WHICH CONFORMS TO `PROTOCOL WITH ASSOCIATED TYPE`
class AnyModelLoader<T>: ModelLoading {
    typealias CompletionHandler = (Result<T, Error>) -> Void

    private let loadingClosure: (CompletionHandler) -> Void

    init<L: ModelLoading>(loader: L) where L.Model == T {
        self.loadingClosure = loader.load(completionHandler:)
    }

    func load(completionHandler: CompletionHandler) {
        self.loadingClosure(completionHandler)
    }
}

class ViewController: UIViewController {
    
    private let modelLoader: AnyModelLoader<MyModel>
    
    init<T: ModelLoading>(modelLoader: T) where T.Model == MyModel {
        self.modelLoader = AnyModelLoader(loader: modelLoader)
        super.init(nibName: nil, bundle: nil)
    }
    
    required init?(coder: NSCoder) {
        fatalError("init(coder:) has not been implemented")
    }
    
    override func viewWillAppear(_ animated: Bool) {
        super.viewWillAppear(animated)
        
        self.modelLoader.load { result in
            switch result {
            case .success(_):
                print("success")
            case .failure(let error):
                print(error.localizedDescription)
            }
        }

        
    }
    
}


```
