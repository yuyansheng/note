//
//  DevABMenuViewController.swift
//  QADDeveloperModule
//
//  Created by harofan on 2022/7/14.
//  Copyright © 2022 Tencent. All rights reserved.
//

import UIKit;
import QADForm;
import QADDeveloperModule;

@objc(QADDevAnchorUtils)
public class DevAnchorUtils: NSObject {
    // HLS
    static let hlsDebugKey = "DevHLSDebugKey"
    @objc public static func shouldShowHLSDebugView() -> Bool {
        return UserDefaults.standard.bool(forKey: hlsDebugKey)
    }
    // 动态中插
    static let dynamicRequestTimeIntervalKey = "DevDynamicRequestTimeIntervalKey"
    @objc public static func shouldChangeDynamicRequestTimeInterval() -> Bool {
        return UserDefaults.standard.bool(forKey: dynamicRequestTimeIntervalKey)
    }
    
    // 如意帖
    static let anchorRequestTimeIntervalKey = "DevAnchorRequestTimeIntervalKey"
    @objc public static func shouldChangeAnchorRequestTimeInterval() -> Bool {
        return UserDefaults.standard.bool(forKey: anchorRequestTimeIntervalKey)
    }
    
}

@objc(QADDevAnchorMenuViewController)
public class DevAnchorMenuViewController: UIViewController, UITableViewDelegate {
    
    private lazy var tableView: UITableView = {
        var tableView = UITableView();
        tableView.dataSource = viewModel
        tableView.delegate = self
        return tableView;
    }()
    
    private lazy var viewModel = DevAnchorMenuViewModel(sectionCount: 1);
    
    public override func viewDidLoad() {
        super.viewDidLoad()
        initUI()
        bindData()
    }
    
    private func switchKeyMap() -> [String : String] {
        return ["是否开启HLS热区色块: ":DevAnchorUtils.hlsDebugKey,
                "动态中插时间间隔是否开启修改: ":DevAnchorUtils.dynamicRequestTimeIntervalKey,
                "如意帖时间间隔是否开启修改: ":DevAnchorUtils.anchorRequestTimeIntervalKey]
    }
    
    func initUI() {
        view.backgroundColor = .white
        title = "点位广告调试"
        view.addSubview(tableView)
        
        tableView.translatesAutoresizingMaskIntoConstraints = false
        tableView.leftAnchor.constraint(equalTo: view.leftAnchor).isActive = true
        tableView.topAnchor.constraint(equalTo: view.topAnchor).isActive = true
        tableView.rightAnchor.constraint(equalTo: view.rightAnchor).isActive = true
        tableView.bottomAnchor.constraint(equalTo: view.bottomAnchor).isActive = true
    }
    
    func bindData() {
        var switchItemList = [RowType]();
        for (title, key) in self.switchKeyMap().sorted(by: <) {
            let item = DevSwitchCellItem(title, key)
            switchItemList.append(Row<DevSwitchCell>(viewData: DevSwitchCellModel(cellItem: item)))
        }
        viewModel.dataArray.append(switchItemList)
        
        viewModel.registerCell(in: tableView)
    }
}

class DevAnchorMenuViewModel: TableViewModel {
    
    override func registerCell(in tableView: UITableView) {
        for rows in dataArray {
            for row in rows {
                tableView.register(row.getCellClass(), forCellReuseIdentifier: row.reuseIdentifier)
            }
        }
    }
}
