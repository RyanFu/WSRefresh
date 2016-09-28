# WSRefresh
仿京东商品详情上下翻页效果，两个tableView显示


# PhotoShoot
![image](https://github.com/Zws-China/WSRefresh/blob/master/%E5%95%86%E5%93%81%E8%AF%A6%E6%83%85%E4%B8%8A%E4%B8%8B%E7%BF%BB%E9%A1%B5/%E5%95%86%E5%93%81%E8%AF%A6%E6%83%85%E4%B8%8A%E4%B8%8B%E7%BF%BB%E9%A1%B5/1.png)
![image](https://github.com/Zws-China/WSRefresh/blob/master/%E5%95%86%E5%93%81%E8%AF%A6%E6%83%85%E4%B8%8A%E4%B8%8B%E7%BF%BB%E9%A1%B5/%E5%95%86%E5%93%81%E8%AF%A6%E6%83%85%E4%B8%8A%E4%B8%8B%E7%BF%BB%E9%A1%B5/2.png)


# How To Use

```ruby

#import "LDJingdongRefreshFooterView.h"
#import "LDJingdongRefreshHeaderView.h"
#import "UIScrollView+LDRefresh.h"

#define kScreenWidth  [UIScreen  mainScreen].bounds.size.width
#define kScreenHeight [UIScreen mainScreen].bounds.size.height

#define FirstTableColor  [UIColor colorWithRed:119/255.0 green:210/255.0 blue:197/255.0 alpha:1.0]
#define SecondTableColor [UIColor colorWithRed:30/255.0 green:171/255.0 blue:201/255.0 alpha:1.0]


self.firstTableView = ({
    UITableView *tableView                 = [[UITableView alloc] init];
    tableView.backgroundColor              = [UIColor groupTableViewBackgroundColor];
    tableView.delegate                     = (id<UITableViewDelegate>)self;
    tableView.dataSource                   = (id<UITableViewDataSource>)self;
    //tableView.separatorStyle               = UITableViewCellSelectionStyleNone;
    tableView.showsVerticalScrollIndicator = NO;
    [self.view addSubview:tableView];

    tableView.frame        = CGRectMake(0, 64, kScreenWidth, kScreenHeight - 64);
    tableView.contentInset = UIEdgeInsetsMake(0, 0, 60, 0);
    tableView;
});

self.secondTableView = ({
    UITableView *tableView                 = [[UITableView alloc] init];
    tableView.backgroundColor              = [UIColor groupTableViewBackgroundColor];
    tableView.delegate                     = (id<UITableViewDelegate>)self;
    tableView.dataSource                   = (id<UITableViewDataSource>)self;
    //tableView.separatorStyle               = UITableViewCellSelectionStyleNone;
    tableView.showsVerticalScrollIndicator = NO;
    [self.view addSubview:tableView];

    tableView.frame = CGRectMake(0, kScreenHeight, kScreenWidth, kScreenHeight - 64);
    tableView;
});

- (void)addRefresh {

    __weak __typeof(self)weakSelf = self;

    self.firstTableView.refreshHeader = [self.firstTableView addRefreshHeader:[[LDRefreshHeaderView alloc] init] handler:^{

        [weakSelf performSelector:@selector(selectAction) withObject:nil afterDelay:1];

    }];

    self.firstTableView.refreshFooter = [self.firstTableView addRefreshFooter:[[LDJingdongRefreshFooterView alloc] init] handler:^{
        [weakSelf loadMoreData];
    }];
    self.firstTableView.refreshFooter.autoLoadMore = NO;

    //_headerView
    self.secondTableView.refreshHeader = [self.secondTableView addRefreshHeader:[[LDJingdongRefreshHeaderView alloc] init] handler:^{
        [weakSelf refreshData];
    }];
}

- (void)loadMoreData {
    __weak __typeof(self)weakSelf = self;
    [UIView animateWithDuration:0.5 animations:^{
        CGRect frame                   = weakSelf.firstTableView.frame;
        frame.origin.y                 = - (kScreenHeight - 64);
        weakSelf.firstTableView.frame  = frame;
    
        frame                          = weakSelf.secondTableView.frame;
        frame.origin.y                 = 64;
        weakSelf.secondTableView.frame = frame;
    } completion:^(BOOL finished) {
        weakSelf.title = @"商品详情";
        [weakSelf.firstTableView.refreshFooter endRefresh];
    }];
}

- (void)refreshData {
    __weak __typeof(self)weakSelf = self;
    [UIView animateWithDuration:0.5 animations:^{
        CGRect frame                   = weakSelf.secondTableView.frame;
        frame.origin.y                 = 64;
        weakSelf.firstTableView.frame  = frame;

        frame                          = weakSelf.secondTableView.frame;
        frame.origin.y                 = kScreenHeight;
        weakSelf.secondTableView.frame = frame;
    } completion:^(BOOL finished) {
        weakSelf.title = @"商品展示";
        [weakSelf.secondTableView.refreshHeader endRefresh];
    }];

}

- (NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section {
    return 20;
}

- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath {

    static NSString *reuseIdentifier = @"reuseIdentifier";
    UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:reuseIdentifier];
    if (!cell) {
        cell = [[UITableViewCell alloc] initWithStyle:UITableViewCellStyleDefault reuseIdentifier:reuseIdentifier];
        [cell setSelectionStyle:UITableViewCellSelectionStyleNone];
    }

    if (tableView == self.firstTableView) {
        cell.backgroundColor = FirstTableColor;
    }else {
        cell.backgroundColor = SecondTableColor;
    }

    cell.textLabel.text = [NSString stringWithFormat:@"%@", @(indexPath.row)];
    return cell;
}


```